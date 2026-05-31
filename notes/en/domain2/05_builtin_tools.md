# Built-in Claude Code Tools

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## The Most Important Distinction

> **Grep searches file CONTENTS. Glob matches file PATHS.**

This is the single most important distinction for exam success.

---

## Grep

Locates **text within files** — function calls, error messages, import statements, variable assignments.

**Use for:**
- Finding all callers of `processLegacyOrder()`
- Finding files that import a specific module
- Locating error messages in source code
- Searching for variable assignments

---

## Glob

Finds **files by name, extension, or directory structure**.

**Use for:**
- Locating test files: `**/*.test.tsx`
- Finding configuration files: `**/config.*`
- Listing all TypeScript files: `**/*.ts`
- Finding files in specific directories

---

## File Modification Tools

### Edit (Default)
Performs targeted replacements using unique text matching. Faster and uses less context than Read + Write.

**When text appears multiple times:** Widen `old_string` with more surrounding context to make it unique, or use `replace_all: true`.

### Read + Write
Reserved for situations where Edit cannot disambiguate the target location.

**Anti-pattern:** Reading all files upfront before understanding relevance — "the single biggest anti-pattern in codebase exploration."

---

## Recommended Exploration Workflow

1. **Grep** to identify relevant entry points
2. **Read** specific files to understand structure
3. **Grep** again to trace usage patterns
4. **Read** only files justified by previous discoveries

Incremental discovery — never read everything upfront.

---

## Key Exam Points

- Grep = search file **contents**; Glob = match file **paths**
- **Edit** is the default for modifications — faster, less context
- Read + Write only when Edit can't pinpoint the location
- Never read all files before understanding what's relevant
- `replace_all: true` handles ambiguous Edit targets

---

## Common Exam Traps

- Using Glob to find function callers (that's Grep's job)
- Using Grep for path-based file searches (that's Glob's job)
- Defaulting to Read + Write for all modifications (use Edit first)
- Reading all source files before understanding relevance
