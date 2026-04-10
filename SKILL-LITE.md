---
name: grounded-ai-lite
description: >
  Forces AI to read and understand the codebase BEFORE responding when the user references their project.
  Optimized version: ~200 lines, ~600 tokens, 80% of the value.
  Trigger: When the user asks about "my project", references specific files/modules, or asks how something works in their codebase.
license: Apache-2.0
metadata:
  author: iammalego
  version: "2.0-lite"
---

## When to Use

Load this skill when ANY of these are true:

- User mentions "mi proyecto", "my project", "este proyecto", "this project"
- User asks about how something works in their codebase
- User references specific files, modules, or components
- User asks for help with code that clearly belongs to their project
- User mentions error/fix/feature in the current workspace

## Critical Rule: READ BEFORE SPEAKING

**NEVER answer based on assumptions. NEVER ask the user "how does it work?" when you can READ the code yourself.**

### The Protocol (6 Steps)

1. **STOP** — Don't formulate any response yet
2. **CHECK MEMORY** — Query engram for cached project structure
3. **READ** — Use glob/read/grep to explore the codebase (incremental if cache exists)
4. **UPDATE CACHE** — Store new/updated file entries
5. **UNDERSTAND** — Build mental model of the relevant parts
6. **RESPOND** — Only after you have actual evidence from the code

## What to Read (Priority Order)

| Priority | Target                                             | Why                                 |
| -------- | -------------------------------------------------- | ----------------------------------- |
| 1        | Cached file structure                              | Skip if cache is fresh (check TTL)  |
| 2        | `README.md`                                        | High-level architecture and purpose |
| 3        | `package.json` / `Cargo.toml` / `go.mod`           | Dependencies, scripts, entry points |
| 4        | Entry point file (`src/index.ts`, `main.py`, etc.) | Application bootstrap and structure |
| 5        | Files mentioned by the user                        | Direct context of the question      |
| 6        | Related modules/components                         | Dependencies of what the user asked |

## Memory-Aware Exploration

### Cold Start (No Cache)

```
User asks about X in their project
    ↓
glob("src/**/*") to see structure  ← Full scan needed
    ↓
Read README.md + package.json (always)
    ↓
If X is a module → read that module's index + related files
If X is a feature → grep for feature keywords
If X is an error → search error message in codebase
    ↓
Build cache with file list + hashes
    ↓
Respond with evidence from ACTUAL CODE
```

### Warm Start (Cache Exists)

```
User asks about X in their project
    ↓
Load cached file structure from engram
    ↓
Check which files changed (compare mtimes or quick hash)
    ↓
Re-scan ONLY changed files (O(k) vs O(n))
    ↓
Read README.md + package.json (always)
    ↓
Use cached knowledge to find related files instantly
    ↓
Respond with evidence from ACTUAL CODE
```

## Key Principle: Incremental Updates

**Never re-read unchanged files.** Use engram to cache:

- File tree structure
- Last modified times
- Content hashes (for change detection)

**Topic keys for caching:**

```
grounded-ai-lite/cache/{project}/files/      # File list + metadata
grounded-ai-lite/cache/{project}/mtimes/    # Modification times
grounded-ai-lite/cache/{project}/hashes/      # Content hashes (optional)
```

## Anti-Patterns (NEVER DO THESE)

| Don't                           | Do Instead                              |
| ------------------------------- | --------------------------------------- |
| "¿Cómo funciona tu proyecto?"   | Read the README and package.json first  |
| "Supongo que tenés..."          | Check if it exists with glob/grep       |
| "Probablemente uses..."         | Read the actual implementation          |
| "¿Querés que te explique X?"    | Just explain it after reading the code  |
| Answer without reading anything | Always read at least README + structure |

## Examples

### Example 1: Bug Investigation (Cold Start)

**User:** "Why isn't my agent making tool calls?"

**AI Process:**

```
1. STOP — Don't answer yet
2. CHECK MEMORY — No cache found for this project
3. READ — Full exploration:
   - glob("src/**/*") → 47 files found
   - read README.md → "Agent for Meta Ads with streaming"
   - read package.json → Uses openai SDK v6
   - grep "tool_calls" → Found in src/agent/agent.ts:233
   - read agent.ts lines 200-250 → Loop uses chat.completions.stream()
4. UPDATE CACHE — Store 47 files + hashes
5. UNDERSTAND — Issue is with tool_choice: 'auto'
6. RESPOND — "I see in src/agent/agent.ts line 233 that the loop
   uses chat.completions.stream(). The issue is that DeepSeek
   sometimes ignores tool_choice when set to 'auto'..."
```

**Response:**

```
I found the issue. Looking at src/agent/agent.ts line 233:

  const stream = this.provider!.client.chat.completions.stream({
      model: this.provider!.model,
      messages: history,
      tools,
      tool_choice: 'auto',  // ← This is the problem
  });

With DeepSeek, 'auto' sometimes causes the model to ignore
available tools. Try changing it to 'required' for specific
operations, or improve your system prompt to make tool
usage more explicit.
```

### Example 2: Architecture Overview (Warm Start)

**User:** "Explain the overall architecture"

**AI Process:**

```
1. STOP — Don't answer yet
2. CHECK MEMORY — Cache found! 47 files, last scan 2 hours ago
3. READ — Incremental:
   - Check mtimes → 2 files modified (src/agent/prompts.ts, src/bot/handler.ts)
   - Re-hash only those 2 files
   - Read README.md (always)
   - Use cached structure to identify layers instantly
4. UPDATE CACHE — Refresh 2 modified entries
5. UNDERSTAND — Clean architecture with DI, 4 layers
6. RESPOND — Architecture diagram + file references
```

**Response:**

```
This is a Clean Architecture Telegram bot with 4 layers:

1. Config Layer (src/config/)
   - env.ts: Zod validation
   - clients.ts: Meta account discovery

2. Bot Layer (src/bot/)
   - handler.ts: Telegram handlers
   - bot.ts: Grammy instance + sessions

3. Agent Layer (src/agent/) ← Core
   - agent.ts: Main LLM loop (line 233: streaming)
   - providers.ts: Multi-provider registry

4. MCP Layer (src/mcp/)
   - client.ts: MCP connection manager
   - tools.ts: Tool routing

Entry point: src/index.ts (PatagonITAgent class)
```

### Example 3: Quick Lookup (Warm Cache)

**User:** "Where is the function that formats responses?"

**AI Process:**

```
1. STOP — Don't answer yet
2. CHECK MEMORY — Cache found with 47 files
3. READ — Targeted:
   - grep "format" in cached file list
   - Found: src/agent/formatter.ts
   - read formatter.ts:15-30 → ResponseFormatter class
4. (No update needed — no changes detected)
5. UNDERSTAND — format() method at line 18
6. RESPOND — Exact location + usage context
```

**Response:**

````
Found it in src/agent/formatter.ts:18

```typescript
class ResponseFormatter {
  async format(text: string): Promise<StructuredResponse> {
    // Converts LLM text → { text, buttons } for Telegram
  }
}
````

Injected into Agent at src/agent/agent.ts:43.

````

## Commands Reference

```bash
# Quick project structure (always start here)
glob "src/**/*" | head -50

# Find specific patterns
grep "pattern" "src/**/*.ts"
grep "functionName" "src/**/*.ts"

# Read key files (always do this)
read README.md
read package.json

# Check for cached knowledge
mem_search(query: "grounded-ai-lite/cache/{project}/")

# Save exploration results
mem_save({
  title: "Project structure cache",
  topic_key: "grounded-ai-lite/cache/{project}/files",
  content: "File tree with 47 entries..."
})
````

## Success Criteria

A response following this skill should:

- ✅ Cite specific files and line numbers
- ✅ Reference actual code, not assumptions
- ✅ Show understanding of project structure
- ✅ Not ask the user to explain their own code
- ✅ Provide evidence from the codebase for every claim

## Why This Matters

**Without this skill:**

```
User: "Why isn't my X working?"
AI: "How is X implemented? Can you show me the code?"
     ↑ You hate this — you're asking the AI to help YOU, not the other way around
```

**With this skill:**

```
User: "Why isn't my X working?"
AI: [Reads code automatically] "I see the issue in src/X.ts:42..."
     ↑ This is what you want — AI investigates, you get answers
```

## Summary

**The entire skill in one sentence:**

> When the user mentions their project, STOP and READ the actual code before responding. Use engram to cache what you learn so you never re-read unchanged files.

That's it. Everything else is implementation details.
