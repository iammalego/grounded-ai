# Example: Architecture Understanding

## Scenario

You join a new team and need to understand their codebase quickly. Instead of reading docs for hours, you ask:

```
"Explain the overall architecture of this project"
```

## What Happens With Grounded AI

### Step 1: Query Classification

```
Intent: deep-explore
Confidence: 98%
Keywords: "explain", "overall", "architecture"
Pattern: explanation_verb + scope_adjective + technical_term
→ Triggers full 4-wave exploration
```

### Step 2: Complete Project Scan

**Wave 1 - Project Structure (50 tokens):**
```
Project: meta-ads-agent
Type: TypeScript/Node.js Telegram Bot
Entry: src/index.ts
Structure: Clean architecture with DI
```

**Wave 2 - Public APIs (200 tokens):**
```
PatagonITAgent (src/index.ts:27)
  ├── start() - Initializes connections
  ├── stop() - Graceful shutdown
  └── Dependencies: McpManager, Provider, Agent, Handlers

Agent (src/agent/agent.ts:182)
  ├── run() - Main loop
  ├── executeConfirmedAction() - Write confirmation
  └── Uses: streaming, tool_calls, validation
```

**Wave 3 - Implementation Details (1000+ tokens):**
```
Architecture Pattern: Clean Architecture + Dependency Injection

Layer 1: Config (src/config/)
  - env.ts: Zod validation, fail-fast
  - clients.ts: Meta API account discovery

Layer 2: Bot Interface (src/bot/)
  - bot.ts: Grammy instance + session management
  - handler.ts: Telegram handlers
  - dashboard.ts: Quick metrics view

Layer 3: Agent Core (src/agent/)
  - agent.ts: LLM loop + tool execution
  - providers.ts: Multi-provider registry (DeepSeek, OpenAI, etc.)
  - prompts.ts: System prompt builder
  - formatter.ts: Response → Telegram format

Layer 4: MCP Integration (src/mcp/)
  - Connects to mlg-meta-mcp server
  - Tool routing by name
  - Read vs Write tool classification
  - Schema sanitization for OpenAI compatibility

Data Flow:
  User (Telegram)
    ↓
  BotHandlers
    ↓
  Agent.run()
    ↓
  McpClientManager.callTool()
    ↓
  mlg-meta-mcp (external MCP server)
    ↓
  Meta Ads API
```

**Wave 4 - Dependencies (500 tokens):**
```
External Dependencies:
• grammy: Telegram bot framework
• @modelcontextprotocol/sdk: MCP client
• openai v6: Streaming API (all providers)
• zod: Runtime validation
• pino: Structured logging

Key Design Decisions:
• Streaming with live feedback (not polling)
• Write confirmation flow (safety)
• Multi-provider AI support
• Auto-discovery of Meta accounts
```

### Step 3: Validation

```
Cross-checking architecture claims:
✓ DI pattern confirmed: constructor injection in all classes
✓ Clean architecture: layers don't bypass each other
✓ Streaming: chat.completions.stream() used
✓ Confirmation flow: __pending_confirmation__ placeholder found
✓ Multi-provider: AIProviderRegistry with 5+ providers

Confidence: 94% (Very High)
```

### Step 4: Response

```
Confidence: 94% 🟢

## Architecture Overview: PatagonIT Agent

This is a **Clean Architecture** Telegram bot for Meta Ads management, 
built with TypeScript and Dependency Injection.

### Layer Structure (inner → outer):

1️⃣ **Config Layer** (src/config/)
   - Environment validation with Zod
   - Meta account auto-discovery
   - Fail-fast on missing env vars

2️⃣ **Bot Layer** (src/bot/)
   - Grammy.js for Telegram API
   - Session management in memory
   - Inline keyboard handlers
   - Live feedback with message editing

3️⃣ **Agent Layer** (src/agent/) ← Core
   - Main loop: LLM → Tools → Response
   - Multi-provider AI support:
     • DeepSeek (default)
     • OpenAI (GPT-4o)
     • Anthropic (Claude)
     • Groq, Gemini
   - Write confirmation flow for safety
   - Streaming with 500ms throttle

4️⃣ **MCP Layer** (src/mcp/)
   - Connects to mlg-meta-mcp server
   - Tool routing by name
   - Read vs Write tool classification
   - Schema sanitization for OpenAI compatibility

### Key Innovations:

• **Live Feedback**: Single message evolves in real-time 
  (spinner → partial text → final + buttons)
  
• **Write Confirmation**: Any destructive action requires 
  explicit user confirmation with reason
  
• **Streaming Architecture**: Uses OpenAI SDK v6 streaming
  for real-time response building

### Data Flow:
```
User → Telegram → BotHandlers → Agent → McpManager
                              ↓
                    LLM (DeepSeek/OpenAI/etc)
                              ↓
                    Meta Ads API (via MCP)
```

**Entry Point**: src/index.ts (PatagonITAgent class)
**Key Files**: src/agent/agent.ts (233 lines of core logic)
**External**: github.com/iammalego/mlg-meta-mcp
```

## Comparison

| Traditional | With Grounded AI |
|-------------|------------------|
| Read README (if exists) | Reads actual structure |
| Ask senior dev | AI investigates for you |
| 2-4 hours to understand | 30 seconds to get overview |
| Outdated docs | Live code analysis |
| Generic explanation | Specific files and lines |

## Tokens Used

- **Full architecture scan**: ~2,500 tokens
- **Equivalent manual reading**: ~50,000+ tokens (entire codebase)

**Savings: 95% fewer tokens, instant understanding**
