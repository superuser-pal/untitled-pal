# Smart Input Discovery Protocol

**Status:** Reference Only - Already Covered in Canonical Pattern

**Source:** workflow.xml discover_inputs protocol

## Overview

This pattern is already fully documented in **workflow-engine-pattern.xml** under the "discover_inputs Protocol" section. It describes how the workflow engine intelligently loads files using three strategies: FULL_LOAD, SELECTIVE_LOAD, and INDEX_GUIDED.

## Why This is a Reference Pattern

The discover_inputs protocol is:
- ✅ Already documented in workflow-engine-pattern.xml (canonical #10)
- ✅ Built into the workflow engine (you don't implement it yourself)
- ✅ Works automatically when you define input_file_patterns

## When to Reference This

**Read this if:**
- You've read workflow-engine-pattern.xml and want deeper implementation details
- You're building custom protocols beyond discover_inputs
- You're debugging input loading issues

**Don't read this if:**
- You just want to use discover_inputs (read workflow-engine-pattern.xml instead)
- You're new to workflows (start with simpler patterns first)

## What's in workflow-engine-pattern.xml

The canonical pattern already covers:
1. **FULL_LOAD strategy** - Load all sharded files
2. **SELECTIVE_LOAD strategy** - Load specific shard using template variable
3. **INDEX_GUIDED strategy** - Load index.md, analyze, intelligently load relevant docs
4. **Fallback to whole documents** - If no sharded version found
5. **Not found handling** - Empty content if no matches

## Personal Automation Relevance

**Already handled** - Just use the workflow engine as documented in workflow-engine-pattern.xml. No additional implementation needed.

## Canonical Reference

See **workflow-engine-pattern.xml**, section: "discover_inputs Protocol"

That pattern provides complete documentation including:
- How each load strategy works
- When to use each strategy
- Examples for budget folders, health logs, goal tracking
- Integration with workflow.yaml
