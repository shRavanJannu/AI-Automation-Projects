# 🤖 Natural Language to SQL Query Agent
### n8n Automation with Google Gemini + Supabase (PostgreSQL)

---

## 📌 Overview

This automation workflow accepts a natural language chat message, interprets it using an AI Agent powered by **Google Gemini**, and converts it into a **SQL query** that is executed against a **Supabase PostgreSQL** database — all orchestrated through **n8n**.

---

## 🏗️ Architecture

```

---

## 🧰 Tech Stack

| Component        | Tool / Service              |
|------------------|-----------------------------|
| Automation       | n8n                         |
| AI Model         | Google Gemini API           |
| Database         | Supabase (PostgreSQL)       |
| Memory           | n8n Simple Memory           |
| Trigger          | Chat Message (n8n Chat)     |

---

## ✅ Prerequisites

Before setting up, make sure you have:

- [ ] **n8n** installed (self-hosted or n8n Cloud)
- [ ] **Google Gemini API Key** — from [Google AI Studio](https://aistudio.google.com/)
- [ ] **Supabase Project** with a PostgreSQL database — from [supabase.com](https://supabase.com/)
- [ ] Your Supabase **DB connection string** (host, port, DB name, user, password)

---

## 🚀 Step-by-Step Setup

### Step 1 — Set Up Your Supabase Database

1. Go to [https://supabase.com](https://supabase.com) and create a new project.
2. Create your tables via the **Table Editor** or SQL editor.
3. Navigate to **Project Settings → Database** and note down:
   - Host
   - Port (default: `5432`)
   - Database name
   - User (`postgres`)
   - Password

> ⚠️ Keep your credentials safe. You'll need them in Step 4.

---

### Step 2 — Get Your Google Gemini API Key

1. Visit [https://aistudio.google.com/](https://aistudio.google.com/)
2. Sign in with your Google account.
3. Click **"Get API Key"** → **"Create API Key"**.
4. Copy and store the key securely.

---

### Step 3 — Install / Open n8n

**Cloud:**
- Sign up at [https://n8n.io](https://n8n.io) and create a new workflow.

---

### Step 4 — Configure Credentials in n8n

#### 4a. Google Gemini Credential
1. In n8n, go to **Settings → Credentials → New Credential**.
2. Search for **"Google Gemini"** (or Google PaLM / Generative AI).
3. Paste your **Gemini API Key**.
4. Save as e.g. `Gemini API`.

#### 4b. Postgres / Supabase Credential
1. In n8n, go to **Settings → Credentials → New Credential**.
2. Search for **"Postgres"**.
3. Fill in the fields:
   - **Host:** your Supabase host (e.g. `db.xxxx.supabase.co`)
   - **Port:** `5432`
   - **Database:** `postgres`
   - **User:** `postgres`
   - **Password:** your Supabase DB password
   - **SSL:** Enable (required for Supabase)
4. Save as e.g. `Supabase Postgres`.

---

### Step 5 — Build the n8n Workflow

#### Node 1 — Trigger: "When Chat Message Received"
1. Add a **Chat Trigger** node.
2. This node listens for incoming chat messages.
3. No additional configuration needed — it acts as the entry point.

---

#### Node 2 — AI Agent
1. Add an **AI Agent** node and connect it to the Chat Trigger.
2. Configure the following sub-components:

**→ Chat Model (Google Gemini)**
- Click the **Chat Model** slot.
- Add a **Google Gemini Chat Model** node.
- Select your saved `Gemini API` credential.
- Choose model: `gemini-1.5-flash` or `gemini-1.5-pro`.

**→ Memory (Simple Memory)**
- Click the **Memory** slot.
- Add a **Simple Memory** node.
- This retains context across multi-turn conversations.

**→ Tool (Postgres + Query Executor)**
- Click the **Tool** slot.
- Add a **Postgres** node:
  - Set operation to `Execute Query`.
  - Select your `Supabase Postgres` credential.
  - Leave the query field dynamic (the AI Agent will populate it).
- Optionally add a **Query Executor** tool node for additional query handling.

---

#### Node 3 — System Prompt for the AI Agent
In the AI Agent node, set a **System Prompt** similar to:

```
You are a SQL expert. The user will ask questions in natural language about the database.
Your job is to:
1. Understand the user's intent.
2. Convert it into a valid PostgreSQL SQL query.
3. Execute the query using the available Postgres tool.
4. Return the result in a friendly, readable format.

Database schema:
- [Table 1]: columns (id, name, ...)
- [Table 2]: columns (id, user_id, ...)

Only run SELECT queries unless explicitly told otherwise.
```

> 📝 Replace the schema section with your actual Supabase table structure.

---

### Step 6 — Test the Workflow

1. Click **"Test Workflow"** or open the built-in chat interface.
2. Type a natural language question, e.g.:
   - *"How many users signed up last week?"*
   - *"Show me the top 5 products by revenue"*
   - *"List all orders with status = pending"*
3. The agent will generate SQL, execute it, and return the results.

---

### Step 7 — Activate the Workflow

1. Once tested, toggle the workflow to **Active** in n8n.
2. The Chat Trigger will now be live and ready to receive messages.

---

## 🔒 Security Best Practices

- **Never expose** your Gemini API Key or DB credentials publicly.
- Use **environment variables** in self-hosted n8n for credentials.
- Restrict the AI to **read-only queries** (SELECT only) unless writes are required.
- Enable **Row Level Security (RLS)** in Supabase for additional protection.
- Use a **dedicated DB user** with limited permissions for this automation.

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Gemini API error | Check API key validity and quota at Google AI Studio |
| Postgres connection failed | Verify host, port, SSL settings; check Supabase firewall |
| Agent returns wrong SQL | Improve the system prompt with more schema details |
| Memory not retaining context | Ensure Simple Memory node is properly linked to the Agent |
| n8n workflow not triggering | Make sure the workflow is set to **Active** |

---

## 📚 Resources

- [n8n Documentation](https://docs.n8n.io/)
- [Google Gemini API Docs](https://ai.google.dev/docs)
- [Supabase Documentation](https://supabase.com/docs)
- [n8n AI Agent Node](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/)
- [n8n Postgres Node](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.postgres/)

---
