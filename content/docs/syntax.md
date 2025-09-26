---
title: "Askl Syntax Reference"
description: "Complete reference for the Askl query language syntax"
weight: 200
---

# Askl Syntax Reference

Askl is a pattern-matching query language designed for source code analysis. It allows you to find functions, trace dependencies, and build custom views of your codebase.

## Overview

An Askl query consists of **statements** that select and filter code symbols (functions, methods, etc.) and define relationships between them through **scopes**.

### Basic Structure

```askl
statement1;
statement2 {
    nested_statement
}
```

## Core Concepts

### 1. Statements

A **statement** is the fundamental unit of an Askl query. Each statement contains:

- **Commands**: One or more verbs that define what to select or filter
- **Scope**: Optional nested statements that define relationships

#### Statement Separation

Use semicolons to separate consecutive statements:

```askl
"foo";          # First statement
"bar"           # Second statement (semicolon optional at end)
```

Without semicolons, verbs belong to the same statement:

```askl
"foo" "bar"     # Single statement with two selector verbs
```

### 2. Scopes and Relationships

Scopes use `{}` syntax to define parent-child relationships:

```askl
"parent_function" {
    "child_function"
}
```

**Important**: Scopes only show results if the actual relationship exists in the code. If `parent_function` doesn't call `child_function`, no results are displayed.

#### Scope Types

- **Callee scope**: `parent { child }` - shows what parent calls
- **Caller scope**: `{ "parent" }` - shows what calls parent
- **Nested scopes**: `a { b { c } }` - multi-level relationships

### 3. Pattern Matching

Askl uses exact, case-sensitive token matching on fully qualified names:

- `"cli.Run"` matches functions containing both "cli" and "Run" tokens
- `"cli"` will NOT match "click" (exact matching)
- Matching works on the complete package path + function name

## Verbs

Verbs are commands that instruct the query engine what to do. They use the syntax:

```askl
@verb(positional_args, key=value)
```

### Core Verbs

#### @select (Function Selection)

Selects symbols whose names match a specific pattern.

**Full syntax:**
```askl
@select(name="cli.Run")
```

**Shortcut syntax:**
```askl
"cli.Run"
```

**Pattern matching rules:**
- Exact, case-sensitive token matching
- Matches against fully qualified names (package.function)
- Must contain ALL specified tokens

**Examples:**
```askl
"main"          # Functions containing "main"
"http.Handler"  # Functions containing both "http" and "Handler"
"user.Create"   # Functions containing both "user" and "Create"
```

#### @ignore (Symbol Filtering)

Excludes symbols matching a pattern from current and nested statements.

**Syntax:**
```askl
@ignore("pattern")
```

**Examples:**
```askl
@ignore("test") "main" {}        # Ignore test functions
@ignore("builtin") @ignore("fmt") "process" {}  # Multiple ignores
```

#### @preamble (Global Configuration)

Applies verbs to the global scope, affecting all subsequent statements.

**Syntax:**
```askl
@preamble
@ignore("builtin")
@ignore("test")

"main"  # This and all following queries ignore builtin and test
```

**Use cases:**
- Global ignore patterns
- Session-wide configuration
- Reducing noise across all queries

#### @forced (Override Relationships)

Forces display of relationships that don't exist in the actual code.

**Syntax:**
```askl
"parent" {
    !"forced_child"
}
```

**When to use:**
- Function pointer calls not detected by analysis
- Complex runtime relationships
- Simplified architectural views
- Working around analysis limitations

## Query Rules and Validation

### Required Elements

Every global statement must contain at least one selection verb at some nesting level.

**Valid examples:**
```askl
"foo" {};           # Direct selection
{"foo"};           # Selection in scope
{{{"foo"}}}        # Deeply nested selection
```

**Invalid example:**
```askl
{{{{}}}}           # No selection anywhere
```

### Statement Structure

```askl
# Multiple statements
"statement1";
"statement2" {
    "nested"
};
"statement3"
```
