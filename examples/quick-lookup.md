# Example: Quick Lookup

## Scenario

You know there's a function somewhere but can't remember which file. Instead of grep-ing, you ask:

```
"Where is the function that formats responses for Telegram?"
```

## What Happens With Grounded AI

### Step 1: Query Classification

```
Intent: quick-lookup
Confidence: 95%
Keywords: "where is", "function", "formats", "responses"
Entities: detected "Telegram" (platform)
→ Triggers 2-wave shallow exploration
```

### Step 2: Fast Search

**Wave 1 - File Discovery (metadata only):**
```
Searching for: format*, response*, telegram*

Candidates found:
• src/agent/formatter.ts - "ResponseFormatter" class
• src/bot/handler.ts - message formatting functions
• src/agent/types.ts - Response types
```

**Wave 2 - Signature Check (no deep reading):**
```
ResponseFormatter (src/agent/formatter.ts:15)
  ├── format(text: string): Promise<StructuredResponse>
  └── formatButtons(actions: Action[]): InlineKeyboardButton[]

BotHandlers (src/bot/handler.ts:45)
  ├── withLiveFeedback(callback)
  └── editMessage(text, parse_mode)
```

### Step 3: Quick Validation

```
Checking formatter.ts:
✓ File exists
✓ Class "ResponseFormatter" exported
✓ Method "format" exists
✓ Return type matches "StructuredResponse" type

Confidence: 96% (Very High - specific match)
```

### Step 4: Response

```
Confidence: 96% 🟢

Found it in **src/agent/formatter.ts:15**

```typescript
class ResponseFormatter {
  async format(text: string): Promise<StructuredResponse> {
    // Converts LLM text → { text, buttons } for Telegram
  }
}
```

**Usage**: Injected into Agent class (src/agent/agent.ts:43)

**Related**:
• src/agent/types.ts:28 - StructuredResponse interface
• src/bot/handler.ts:127 - withLiveFeedback() uses it
```

## Speed Comparison

| Method | Time | Accuracy |
|--------|------|----------|
| Manual grep | 30-60 seconds | High (if you know patterns) |
| IDE search | 10-20 seconds | Medium (symbol indexing) |
| **Grounded AI** | **3-5 seconds** | **Very High (validated)** |

## Tokens Used

- **Quick lookup**: ~300 tokens
- **Finding + explaining**: ~500 tokens

**Efficiency: Instant, validated answer**

## Why It's Better Than grep

1. **Understands context**: Knows "format for Telegram" means looking in `src/agent/`
2. **Validates**: Confirms the function actually exists and is exported
3. **Connects**: Shows where it's used (Agent class)
4. **Types**: Includes type information (StructuredResponse)
5. **No false positives**: Doesn't match "format" in comments or strings
