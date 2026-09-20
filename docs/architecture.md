# Workflow Architecture

## Overview

This workflow combines AI message analysis, an AI Agent, retrieval-augmented generation (RAG), conversation memory, conditional escalation, structured logging, and human notification.

## Flow

### 1. Customer message
The workflow starts with the **When chat message received** node.

### 2. Initial AI analysis
The message is passed to **Message a model**, which uses Google Gemini. The workflow then uses **Edit Fields** to prepare information for the next stage.

### 3. AI Agent
The prepared customer message is sent to the **AI Agent**.

The agent has access to:
- **Google Gemini Chat Model** for language-model responses.
- **Supabase Vector Store** for business knowledge retrieval.
- **Simple Memory** for maintaining conversational context.

### 4. RAG retrieval
The Supabase Vector Store is connected to **Embeddings Google Gemini**, allowing semantic retrieval against the configured knowledge base.

### 5. Escalation decision
After the AI Agent processes the message, the result is passed to the **If** node. The conditional branch determines whether the conversation requires escalation.

### 6. Escalation logging
When escalation is required, **Create a row** stores the relevant escalation information in Supabase.

### 7. Human notification
The Supabase logging step is followed by **Send a message**, which sends the escalation notification through Gmail.

## Node map

```text
When chat message received
          |
          v
    Message a model
          |
          v
      Edit Fields
          |
          v
       AI Agent <------ Google Gemini Chat Model
          ^   ^
          |   |
          |   +-------- Supabase Vector Store
          |                    ^
          |                    |
          |          Embeddings Google Gemini
          |
          +------------ Simple Memory
          |
          v
          If
          |
          +------> Create a row ------> Send a message
```

## Configuration required

The sanitized workflow intentionally does not contain production credentials or private identifiers.

Configure your own:
1. Google Gemini credentials
2. Supabase credentials
3. Supabase Vector Store and knowledge-base settings
4. Gmail credentials
5. Support notification destination
6. Environment-specific database/table settings

## Security

Never publish API keys, OAuth tokens, database passwords, private webhook URLs, production credentials, or personal/client-sensitive data.
