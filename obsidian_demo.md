# Obsidian Features Demo

## Contents
- [[#Heading Links]]
- [[#Cross-File Links]]
- [[#Block References]]
- [[#Embedding Content]]
- [[#Callouts]]
- [[#Tags and Metadata]]


---

# Test heading

## Heading Links

You can link to any heading in the current file using `[[#Heading Name]]`.

For example, jump to [[#Callouts]] or [[#Block References]].

You can also give the link custom display text: [[#Tags and Metadata|skip to the tags section]].

## Cross-File Links

Link to another file in the vault by name:

- [[obsidian_demo_target]] — links to the target demo file
- [[obsidian_demo_target|Study Guide Target Page]] — same link with display text

Link to a specific heading inside that file:

- [[obsidian_demo_target#Study Tips|Study Tips]] — jumps to the Study Tips section
- [[obsidian_demo_target#Common Pitfalls|Common Pitfalls]] — jumps to Common Pitfalls
- [[obsidian_demo_target#Pitfall 1 — Highlighting Instead of Thinking|First pitfall]] — deep link to a sub-heading

## Block References

Any paragraph can be given a block ID by appending `^block-id` at the end.

This is a key concept worth bookmarking. ^key-concept

Now you can reference it from anywhere in this file: [[#^key-concept]]

Or from another file: `[[obsidian_demo#^key-concept]]`

Here is another block you might want to reference later:

> The best way to learn is to build something, break it, then understand why it broke. ^learning-principle

Reference it: [[#^learning-principle]]

You can also reference blocks in the target file:

- [[obsidian_demo_target#^spaced-repetition|Spaced repetition paragraph]] — a specific paragraph
- [[obsidian_demo_target#^techniques-table|Techniques table]] — a specific table

## Embedding Content

Use `!` before a link to embed (transclude) content inline instead of just linking:

Embed a full file:
```
![[obsidian_demo_target]]
```

Embed just one heading section:
```
![[obsidian_demo_target#Quick Reference]]
```

Embed a single block:
```
![[obsidian_demo_target#^techniques-table]]
```

## Callouts

Callouts are styled blocks useful for highlighting information:

> [!tip] Obsidian Shortcut
> Press `Ctrl+O` to quick-open any file by name.

> [!warning] Block IDs
> Block IDs must be unique within a file. Use lowercase and hyphens.

> [!info] Outline Pane
> Open with `Ctrl+P` → "Outline: Show outline" to see all headings in a sidebar.

> [!example] Table of Contents
> The [[#Contents]] section at the top of this file is a manual TOC built with heading links.

## Tags and Metadata

You can tag any note inline with `#tag-name`:

This section is about #obsidian and #note-taking.

Or use YAML frontmatter at the very top of a file:

```yaml
---
tags: [obsidian, demo, reference]
aliases: [Obsidian Cheatsheet]
---
```

The `aliases` field lets you link to this file by alternative names — e.g. `[[Obsidian Cheatsheet]]` would resolve here.

---

## Summary

| Feature | Syntax | Example |
|---|---|---|
| Link to file | `[[filename]]` | [[obsidian_demo_target]] |
| Link with text | `[[file\|text]]` | [[obsidian_demo_target\|Target Page]] |
| Heading (same file) | `[[#Heading]]` | [[#Callouts]] |
| Heading (other file) | `[[file#Heading]]` | [[obsidian_demo_target#Study Tips]] |
| Block ref (same) | `[[#^block-id]]` | [[#^key-concept]] |
| Block ref (other) | `[[file#^block-id]]` | [[obsidian_demo_target#^spaced-repetition]] |
| Embed | `![[file]]` | `![[obsidian_demo_target]]` |
| Tag | `#tag-name` | #obsidian |
