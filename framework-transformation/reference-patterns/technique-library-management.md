# Technique Library Management Pattern

**Status:** Reference Only - Already Implemented in Canonical Pattern

**Source:** advanced-elicitation.xml and advanced-elicitation-methods.csv

## Overview

This pattern describes how to manage CSV-based technique libraries for content enhancement. It's already fully implemented in **advanced-elicitation-pattern.xml** (canonical #11).

## Why This is a Reference Pattern

Technique library management is:
- ✅ Already implemented in advanced-elicitation-pattern.xml
- ✅ Built into the advanced elicitation workflow
- ✅ Extensible (you can add rows to the CSV)

## When to Reference This

**Read this if:**
- You want to create your own custom CSV-based method libraries
- Building domain-specific technique collections beyond elicitation
- Implementing similar pattern matching systems

**Don't read this if:**
- You just want to use the 50+ elicitation techniques (use advanced-elicitation-pattern.xml)
- You're new to personal automation (canonical pattern is sufficient)

## What's in advanced-elicitation-pattern.xml

The canonical pattern already covers:
1. **CSV structure** - id, category, method_name, description, output_pattern
2. **Smart selection** - Context-aware method selection
3. **Dynamic loading** - Load techniques at runtime
4. **Extensibility** - Add custom methods to CSV
5. **Category organization** - 9 categories (core, collaboration, scenario, etc.)

## Extending the Library

**To add your own techniques:**

1. Open `advanced-elicitation-methods.csv`
2. Add row:
```csv
51,personal,Life Wheel Balance,life areas → gaps → rebalance,Evaluate balance across life domains
```
3. Technique now available in elicitation menu

**CSV format:**
```
id,category,method_name,output_pattern,description
```

## Personal Automation Relevance

**Already handled** - The advanced-elicitation-pattern.xml provides a complete CSV-based technique library. Just use it and extend it if needed.

## Canonical Reference

See **advanced-elicitation-pattern.xml**, section: "Method Library Categories"

That pattern documents:
- All 50+ existing techniques
- How to use them for personal automation
- Examples for goals, reviews, budgets, career planning
- How the CSV-driven system works
