---
name: supacortex
description: Manage bookmarks, conversation memory, and identity context using the Supacortex CLI. Use when saving bookmarks, summarizing chat sessions, storing user preferences, or recalling previous conversations and saved content.
allowed-tools: Bash(scx bookmarks *) Bash(scx conversation *) Bash(scx identity *)
compatibility: Requires the scx CLI to be installed and authenticated (scx login)
metadata:
  author: monorepo-labs
  version: "1.0"
---

# Supacortex — CLI Skill

Supacortex is a personal knowledge workspace. Three CLI commands: bookmarks, conversations, and identity.

## Setup

```bash
npm i -g @supacortex/cli
scx login
```

## 1. Bookmarks (`scx bookmarks`)

Save and search bookmarks (tweets, links, YouTube videos).

### Commands

#### List bookmarks

```bash
scx bookmarks list [--search "<query>"] [--type <tweet|link|youtube>] [--limit <n>] [--offset <n>] [--pretty]
```

#### Add a bookmark

```bash
scx bookmarks add <url> [--pretty]
```

#### Get a bookmark by ID

```bash
scx bookmarks get <id> [--pretty]
```

#### Delete a bookmark

```bash
scx bookmarks delete <id> [--pretty]
```

### When to use bookmarks

- User asks to save a link or tweet
- User wants to search their saved content
- When you need to reference previously saved URLs or tweets

## 2. Conversations (`scx conversation`)

Save summaries of AI chat sessions. Every conversation has a **tier** that determines its depth.

### Tiers

| Tier | When to use | Content format |
|------|-------------|----------------|
| `brief` | Throwaway queries, quick lookups | Single sentence: "Asked about JSON parsing in Bun" |
| `summary` | Most working sessions | 3-8 bullet points covering what was discussed, decided, and found |
| `detailed` | Deep sessions with architectural decisions, research findings | Full structured document with reasoning, code snippets, follow-ups |

### Commands

#### Save a conversation

```bash
scx conversation add "<content>" --tier <brief|summary|detailed> [--title "<title>"] [--metadata '<json>'] [--pretty]
```

The `--tier` flag is required. It maps to memory types: `conversation_brief`, `conversation_summary`, `conversation_detailed`.

**Examples:**

```bash
# Brief — one sentence
scx conversation add "Helped debug CORS issue in Hono API" --tier brief

# Summary — bullet points
scx conversation add "- Set up memory table with tsvector search
- Added triggers for auto search vector generation
- Created API routes for CRUD
- Decided on hybrid schema approach" \
  --tier summary \
  --title "Memory system setup" \
  --metadata '{"source": "claude-code"}'

# Detailed — full document
scx conversation add "## Memory Architecture Decision..." --tier detailed --title "Memory layer brainstorm"
```

#### List conversations

```bash
scx conversation list [--search "<query>"] [--tier <brief|summary|detailed>] [--limit <n>] [--offset <n>] [--pretty]
```

#### Get a conversation by ID

```bash
scx conversation get <id> [--pretty]
```

#### Update a conversation

```bash
scx conversation update <id> [--title "<title>"] [--content "<content>"] [--tier <tier>] [--metadata '<json>'] [--pretty]
```

#### Delete a conversation

```bash
scx conversation delete <id> [--pretty]
```

### When to save conversations

- At the end of a productive session — summarize what was done
- When the user says "save this session" or "remember this"
- After making architectural decisions worth preserving
- When the user asks "what did we work on?" — search past conversations first

## 3. Identity (`scx identity`)

Store persistent context about the user so AI agents can be useful without the user repeating themselves.

### Categories

| Category | What it stores | How often it changes |
|----------|---------------|---------------------|
| `core` | Name, location, what they do, background | Rarely |
| `goals` | Current focus, bigger picture objectives | Every few months |
| `preferences` | Tech choices, communication style, workflow | Discovered over time |
| `interests` | Topics they're drawn to, areas of curiosity | Keeps growing |

### Commands

#### Add an identity entry

```bash
scx identity add "<content>" [--title "<title>"] [--category <core|goals|preferences|interests>] [--metadata '<json>'] [--pretty]
```

**Examples:**

```bash
# Core profile
scx identity add "Yogesh, solo founder based in Nepal. Building Supacortex and Supalytics." \
  --title "Core profile" \
  --category core

# Goals
scx identity add "Ship Supacortex memory layer, then CLI and API as the product" \
  --title "Current focus" \
  --category goals

# Preferences
scx identity add "Next.js, Drizzle, Bun, Postgres, Tailwind. Casual and direct communication." \
  --title "Tech and communication preferences" \
  --category preferences

# Interests
scx identity add "AI agent memory systems, indie hacking, self-hosting, vectorless RAG" \
  --title "Current interests" \
  --category interests
```

#### List identity entries

```bash
scx identity list [--search "<query>"] [--category <core|goals|preferences|interests>] [--limit <n>] [--pretty]
```

#### Get an entry by ID

```bash
scx identity get <id> [--pretty]
```

#### Update an entry

```bash
scx identity update <id> [--title "<title>"] [--content "<content>"] [--category <category>] [--metadata '<json>'] [--pretty]
```

#### Delete an entry

```bash
scx identity delete <id> [--pretty]
```

### When to use identity

- User shares something about themselves worth remembering ("I just switched to Stripe", "I'm based in Nepal")
- User explicitly asks to save identity info ("remember that I prefer Bun over Node")
- At initial setup — help the user create their core profile
- When you need user context — fetch identity entries before responding to personalize the answer

## Metadata

Metadata is freeform JSON passed via `--metadata`. The AI decides what to store. Common fields:

- `source` — where this was captured ("claude-code", "chatgpt", "opencode")
- `tags` — array of topic tags
- `project` — which project the conversation was about
- `category` — for identity entries: "core", "goals", "preferences", "interests"

## Consistent verbs across all commands

All three commands (`bookmarks`, `conversation`, `identity`) support: `list`, `add`, `get`, `delete`.

`conversation` and `identity` also support: `update`.

All commands output JSON by default (optimized for AI agents). Use `--pretty` for human-readable output.
