# hacks&tricks

A collection of command-line tricks and workflow hacks. Reference pages where code and documentation coexist — written for engineers who want to understand what they're running.

## Style Guide

These pages are **reference material** that teach. Code-forward, but explanatory enough that a junior engineer can follow along and learn.

### Structure

Every page follows this pattern:

```
# Title

> One-line summary of what this does.

## Code

The command or script itself. Show it immediately.

## What It Does

Break down each part of the command. Explain flags, syntax, and why things work the way they do.

## Usage

How to run it. Real examples with expected output.

## Notes

- Bullets for caveats, edge cases, platform differences
- Things that might trip someone up
```

### Principles

1. **Code first** — The main code block appears near the top, before explanations.
2. **Then explain** — Break down what each piece does. Don't assume familiarity with obscure flags or syntax.
3. **Flat hierarchy** — Prefer `##` sections. Use `###` sparingly, only for true subsections.
4. **Bullets for details** — Notes, caveats, and tips are bullet points. Paragraphs only when explaining a concept.
5. **Show the why** — When something isn't obvious (like regex, escaping, or shell tricks), explain it.
6. **Include edge cases** — What breaks? What permissions are needed? What platforms does it work on?

### Tone

- Direct and clear
- Explain concepts a junior engineer might not know, but don't be condescending
- No "we", no "let's", no rhetorical questions
- Assume the reader can code but might not know this specific tool or pattern

### Naming

Files: `lowercase-with-dashes.md`

## Commands

```bash
# Preview locally
open *.md
```
