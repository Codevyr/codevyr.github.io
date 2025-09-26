---
title: "Askl Query Language"
description: "Learn Askl syntax "
weight: 200
---

Askl is a pattern matching source code query language.

## Query Structure

### Statements and Scopes

Askl queries consist of **statements**, each containing:

- **Command**: A sequence of verbs (e.g., function filters)
- **Scope**: Child statements that define relationships

Use semicolon to indicate that consecutive verbs belong to different statements:

```
"foo";
"bar"

/* vs */

"foo"
"bar"
```

### Understanding Scopes

The `{}` syntax creates parent-child relationships:

```askl
"foo" {"bar"}
```

This query:
1. Finds functions matching "foo"
2. Finds functions matching "bar" 
3. **Only shows results if "foo" actually calls "bar"**

If "foo" and "bar" both exist but "foo" doesn't call "bar", no symbols will be displayed.

### Verb System

Function names like `cli.Run` are shortcuts for the `@filter` verb:

```askl
cli.Run          # Shortcut
@filter("cli.Run") # Full syntax
```

### Verbs

Verbs instruct statement what to do. The most important role for the verbs is to filter the symbols (e.g. functions) that are of interest to display. A generic has the following syntax:

```
@verb(positional1, positionalN, key1=value1, keyN=valueN)
```

where `@verb` is the name of the verb, followed by a sequence of positional and named parameters.

#### Filter

Filter verb select the symbols whose name matches a specific pattern. The generic syntax looks as follows:

```
@filter("cli.Run")
```

As a shortcut, one can just write:

```
"cli.Run"
```

This verb will select functions that have tokens `cli` and `Run` in its fully qualified name. The tokens are match exactly and case-sensitively.

Each global-scoped statement needs to have at least one filter verb at some level of nesting.

For example the following statements are valid:

```
"foo" {};
{"foo"};
{{{"foo"}}}
```

The following statement is not valid:

```
{{{{}}}}
```

#### Ignore

Ignore verb instructs the current statement and all nested statement to ignore the symbols that match the pattern. Pattern matching works the same way as for the filter verb. Multiple ignore verbs can be present in the same command.

***Example***. The statement below matches function `tar` and all function called by `tar`, except `foo` and `bar`.

```
@ignore("foo") @ignore("bar") "tar" {}
```

#### Preamble

Preamble verb lets to apply all subsequent verbs to the global scope. Preamble is useful, for example, to define globally ignored symbols.

The example below ignores all symbols whose name contains tokens `builtin` and `fmt` globally:

```
@preamble
@ignore("builtin")
@ignore("fmt")
```

#### Forced

Sometimes index does not show an existing relationship between functions. If it is a bug, please report it, but more often this happens, because a function calls another function through a function pointer determined at runtime. Alternatively, the source code can be so complicated that visualizing it exactly does not help to understand the logic.

In both situations, you can tell the query engine to "imagine" that one function is called by another function.

***Example***. The example below will show connection between functions `foo` and `bar` even if `foo` does not call `bar` in the actual code.

```
"foo" {
    !"bar"
}
```

