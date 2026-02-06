# Bash Forge

Bash Forge (`bf`) is a macro-based compilation and packaging system for
Bash that adds strong namespace protection, structured composition, and
reproducible distribution to large-scale shell systems.

It is designed for long-lived research, infrastructure, and automation
codebases where plain Bash scripting becomes difficult to maintain.

Bash Forge is **not** a Bash parser or interpreter. It is a controlled
macro processor that rewrites a small, well-defined set of idioms into
standard, portable Bash.

------------------------------------------------------------------------

## Design Philosophy

-   Deterministic builds and reproducibility
-   Strong namespace isolation
-   Minimal syntax surface
-   Rewrite-based compilation (no full Bash parsing)
-   Single-file distributable artifacts
-   Long-term maintainability

Bash Forge emphasizes clarity and structural correctness over
convenience shortcuts.

------------------------------------------------------------------------

## Core Concepts

### 1. Namespace Protection

Bash Forge extends identifiers with explicit namespaces to prevent
collisions and enable modular composition.

Canonical form:

    (<namespace>)<type><name>

Examples:

    (@com.acme:db.mysql):connect
    (@):main
    (@@)_version
    (@org.agricommons:soil)/schema.json

Supported namespace forms:

Supported namespace forms:

| Form                   | Meaning                  |
|------------------------|--------------------------|
| `(@com.acme)`          | Reverse-DNS root         |
| `(@com.acme:db.mysql)` | Package                  |
| `(@)`                  | Current file package     |
| `(@@)`                 | System namespace         |
| `(.)`                  | Function-local namespace |

Supported identifier types:

| Type                | Meaning   |
|---------------------|-----------|
| `:<name>`           | Function  |
| `_<name>`           | Variable  |
| `/<path>`           | Path      |
| `:<class>:          | Class     |
| `:<class>:<method>` | Method    |


------------------------------------------------------------------------

### 2. Source Tree Model

Projects are rooted by a `.bfroot` file.

Directory traversal uses:

-   `@init.bash` for directory initialization
-   `*.bash` for compile-time configuration
-   `*.sh` for program code
-   `.shc` / `.shx` for encrypted sources

Directories beginning with `@` are treated as data payloads.

Traversal order:

1.  `@init.bash`
2.  Other `*.bash`
3.  `*.sh`
4.  Recurse

------------------------------------------------------------------------

### 3. Compilation Contexts

Bash Forge operates with multiple controlled contexts:

  Context             |Purpose
  --------------------|-------------------------------
  Init Context        |Directory-level configuration
  Config Context      |Compile-time behavior
  Code Context        |Program definitions
  Annotation Context  |Code transformation

Each context enforces strict rules to preserve determinism.

------------------------------------------------------------------------

### 4. Annotations

Annotations are compile-time operations invoked via the `:` builtin.

    : import com.acme.math as m
    : export main

Annotations may modify generated code through controlled rewrite
operations.

Compilation occurs in two phases:

1.  Expansion without annotation execution
2.  Annotation-driven modification

------------------------------------------------------------------------

### 5. Object System

Bash Forge provides a lightweight object model built on macro expansion.

Features include:

-   Class constructors
-   Methods
-   Field access
-   Chaining

Example:

    (@):Person:()
    {
       + extends (@):Entity
    }

This subsystem is currently experimental.

------------------------------------------------------------------------

### 6. Lambdas and Plugins

Anonymous code blocks can be embedded as callable objects.

    local sum=({sh:
       echo $(($1 + $2))
    })

Used primarily for plugin-style extensibility.

------------------------------------------------------------------------

### 7. Redirection Layer

Symbolic file descriptors are provided for structured I/O.

  Symbol     |Descriptor
  -----------|------------
  `(<in)`    |stdin
  `(>out)`   |stdout
  `(>err)`   |stderr
  `(>data)`  |data
  `(>log)`   |log

------------------------------------------------------------------------

### 8. Try / Catch / Throw

Bash Forge provides structured error handling through controlled
aliases.

    :try { ... }
    :catch { ... }
    :throw

Implemented using `ERR` traps and scoped execution contexts.

------------------------------------------------------------------------

## Build Process

High-level compilation pipeline:

1.  Root discovery
2.  Directory traversal
3.  Init processing
4.  Config sourcing
5.  Macro expansion
6.  Annotation execution
7.  Artifact emission

Output is typically a single, standalone Bash script.

------------------------------------------------------------------------

## Tooling

The primary interface is the `bf` executable.

Planned subcommands:

-   `bf build`
-   `bf extract`
-   `bf inspect`
-   `bf patch`
-   `bf fork`
-   `bf cache`

All functionality is exposed through a unified CLI.

------------------------------------------------------------------------

## Indirection Table

Compiled artifacts embed an indirection table that maps short internal
symbols to fully-qualified namespaces.

Benefits:

-   Compact runtime identifiers
-   Refactoring support
-   Source reconstruction
-   Patchability

Optional "final form" builds may strip this table.

------------------------------------------------------------------------

## Security and Reproducibility

Bash Forge emphasizes:

-   Restricted compile-time execution
-   Controlled sourcing of `.bash` files
-   Environment normalization
-   Manifest recording

Future versions will support hermetic build modes.

------------------------------------------------------------------------

## Project Status

Bash Forge is in early active design and prototyping.

Current focus:

-   Namespace model stabilization
-   Traversal semantics
-   Annotation framework
-   Artifact format

Public API stability is not yet guaranteed.

------------------------------------------------------------------------

## License

To be determined.

------------------------------------------------------------------------

## Contact

Project maintained by Steve Amerige.

Affiliated with AgriCommons Foundation and NC State University research
initiatives.

