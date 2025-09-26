---
title: "Getting Started with Codevyr"
description: "Learn Askl query language basics and start analyzing code with Codevyr"
weight: 100
---

Welcome to Codevyr! This guide will teach you the basics of the Askl (Ask Language) query syntax to analyze and visualize your code dependencies.

## What is Codevyr?

Codevyr is a source code analysis platform that uses the Askl query language to help you:

- **Find Functions** - Locate specific functions across your codebase
- **Trace Dependencies** - See what calls what and build dependency graphs
- **Filter Results** - Focus on relevant parts of your code
- **Visualize Architecture** - Understand code relationships through interactive graphs

## Getting Started

### Access the Platform

Visit **[ui.codevyr.com](https://ui.codevyr.com)** to start using Codevyr.

**Note**: Currently adding new projects is not supported. You can test out Codevyr with pre-existing index of Kubernetes 1.32.3.

1. Start writing Askl queries in the query editor
2. Explore the generated dependency graphs

## Askl Query Language Basics

Askl (Ask Language) is Codevyr's query language for finding and filtering functions in your codebase.

### Basic Queries

#### 1. Simple Function Matching

Find all functions named "main":

```askl
"main"
```

This matches any function with "main" in its name across your entire codebase.

#### 2. Qualified Function Names

Find functions with specific package and function names:

```askl
"cli.Run"
```

This matches functions where:
- The full package path contains "cli"
- The function name contains "Run"

**Note**: Matching is context-sensitive and exact - "cli" will not match "click".

### Dependency Queries

#### 3. Show Function Dependencies (Callees)

Find what functions `cli.Run` calls:

```askl
"cli.Run" {

}
```

This shows `cli.Run` and all functions called by `cli.Run`.

#### 4. Show Function Callers

Find what functions call `cli.Run`:

```askl
{"cli.Run"}
```

This shows all functions that call `cli.Run`.

### Filtering with @ignore

Remove uninteresting functions from results:

```askl
@ignore("builtin")
main
```

This finds all "main" functions but ignores any with "builtin" in their name.

#### Global Ignore (Preamble)

Make ignore rules apply to all queries in your session:

```askl
@preamble
@ignore("builtin")
@ignore("test");

main
cli.Run
```

Place `@ignore` statements at the beginning to filter globally.

## Practical Examples

### Example 1: Explore Main Functions

```askl
"main" {
  
}
```

Shows all `main` functions and what they call.

## Tips for Success

### 🎯 Start Simple

1. Begin with basic function searches: `main`, `init`, `New`
2. Add scopes gradually to explore dependencies
3. Use `@ignore` to reduce noise

### 🔍 Effective Filtering

- **Package-level**: `package.Function`
- **Multiple terms**: `user.Create` (must contain both "user" and "Create")
- **Ignore patterns**: `@ignore("builtin")` to skip built package

### 📊 Reading Results

- **Nodes**: Represent matched functions
- **Edges**: Show function calls (who calls whom)

## Next Steps

Once you're comfortable with basic queries:

1. **Experiment** with complex nested scopes
2. **Combine** multiple filters and ignore patterns
3. **Explore** the interactive graph features
4. **Share** interesting queries with your team

## Need Help?

### 🆘 Common Issues

**Q: My query returns too many results**
A: Add more specific filters or use `@ignore` to reduce noise

**Q: My query returns nothing**
A: Check if parent functions actually call child functions in scopes

**Q: Function names don't match**
A: Remember matching is exact - check spelling and case

### 📚 Learn More

- Experiment with the interactive platform at [ui.codevyr.com](https://ui.codevyr.com)
- Try different query patterns on sample projects
- Use the graph controls to navigate complex results

---

**[Start querying your code now →](https://ui.codevyr.com)**