# Example: Bug Investigation

## Scenario

You're working on a Telegram bot and users report that the agent isn't making tool calls. Instead of explaining your code to the AI, you just ask:

```
"Why isn't my agent making tool calls?"
```

## What Happens With Grounded AI

### Step 1: Query Classification

The AI classifies your query:
```
Intent: error-trace
Confidence: 92%
Keywords: "isn't", "making", "tool calls"
Pattern: negative_auxiliary_verb + technical_term
```

### Step 2: Memory Check

```
Loading cached structure for project: meta-ads-agent
Cache age: 2 hours (within 24h TTL)
Files changed since last scan: 0
→ Using cached structure (0ms)
```

### Step 3: Strategic Reading

Based on "error-trace" intent, the AI:

**Wave 1 (Metadata):**
- Reads `package.json` → finds `openai` SDK v6
- Reads `README.md` → learns about streaming architecture

**Wave 2 (Signatures):**
- Scans `src/agent/agent.ts` → finds `run()` method
- Finds `tool_calls` handling in signature

**Wave 3 (Deep Dive):**
- Reads `agent.ts` lines 207-356:
  ```typescript
  // Line 233: The actual call
  const stream = this.provider!.client.chat.completions.stream({
      model: this.provider!.model,
      messages: history,
      tools,              // ← Tools passed here
      tool_choice: 'auto', // ← LLM decides
  });
  ```

**Wave 4 (Dependencies):**
- Checks `src/agent/providers.ts` → which model is configured?
- Finds: `AI_PROVIDER=deepseek` with model `deepseek-chat`

### Step 4: Validation

```
Cross-checking findings:
✓ Import 'openai' exists in package.json
✓ Function 'chat.completions.stream' exists in SDK v6
✓ 'tools' parameter is valid (checked OpenAI docs)
✓ 'tool_choice' is valid enum value
✓ DeepSeek API supports tool calling (2025-01 update)
⚠ DeepSeek tool_choice behavior differs from OpenAI
```

### Step 5: Response with Evidence

```
Confidence: 87% (High)

I found the issue in your agent. Looking at src/agent/agent.ts 
line 233, you're using:

  tool_choice: 'auto'

With DeepSeek (configured in providers.ts), the 'auto' value 
sometimes causes the model to ignore available tools, especially 
when the prompt is ambiguous about whether a tool is needed.

**Evidence:**
• File: src/agent/agent.ts:233
• Provider: src/agent/providers.ts:47 (DeepSeek)
• SDK: openai v6 with streaming API

**Recommended fix:**
Change tool_choice to 'required' for specific operations, or 
improve your system prompt to make tool usage more explicit.

[Button: Show provider config] [Button: View agent.ts]
```

## Key Differences

| Without Skill | With Skill |
|---------------|------------|
| "¿Cómo está tu agente?" | Directly reads your code |
| Generic suggestions | Specific line numbers |
| No context awareness | Knows you use DeepSeek |
| Assumes standard behavior | Validates against actual config |

## Tokens Used

- **Cold start** (first time): ~2,000 tokens
- **Warm start** (subsequent): ~200 tokens
- **Without skill** (reading everything): ~15,000 tokens

**Savings: 87-99% fewer tokens**
