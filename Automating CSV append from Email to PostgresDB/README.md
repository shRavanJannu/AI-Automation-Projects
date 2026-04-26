# 📧➡️🗄️ Gmail CSV to PostgreSQL — n8n Automation Workflow

Automatically extract CSV attachments from Gmail emails and insert the data into a **PostgreSQL** database (via **Supabase**) using [n8n](https://n8n.io/).

---

## 🔄 Workflow Overview

```
Gmail Trigger  →  Extract from CSV  →  Insert Rows in PostgreSQL
```

| Step | Node | Description |
|------|------|-------------|
| 1 | **Gmail Trigger** | Watches for new emails with CSV attachments |
| 2 | **Extract from File** | Parses the CSV attachment into structured rows |
| 3 | **Insert rows in a table** | Inserts each row into a Supabase PostgreSQL table |

![Workflow Screenshot]()

---

## 🛠️ Tech Stack

- **[n8n](https://n8n.io/)** — Open-source workflow automation
- **Gmail** — Email trigger source
- **[Supabase](https://supabase.com/)** — PostgreSQL database (hosted)
- **PostgreSQL** — Target database for CSV data

---

## 📋 Prerequisites

- n8n instance (self-hosted or [n8n Cloud](https://app.n8n.cloud/))
- A Google account with Gmail API access
- A [Supabase](https://supabase.com/) project with a table matching your CSV schema
- n8n credentials configured:
  - **Gmail OAuth2**
  - **PostgreSQL** (using Supabase connection string)

---

## 🚀 Setup Instructions

### 1. Clone this repository



### 2. Import the workflow into n8n

1. Open your n8n instance
2. Go to **Workflows** → **Import from File**
3. Select `workflow.json` from this repository
4. Click **Import**

### 3. Configure Credentials

#### Gmail
1. In n8n, go to **Credentials** → **New** → **Gmail OAuth2 API**
2. Follow the OAuth2 setup with your Google Cloud project
3. Assign the credential to the **Gmail Trigger** node

#### Supabase / PostgreSQL
1. In your Supabase project, go to **Settings** → **Database**
2. Copy the **Connection String** (URI format)
3. In n8n, go to **Credentials** → **New** → **PostgreSQL**
4. Paste the connection string (replace `[YOUR-PASSWORD]` with your DB password)
5. Assign the credential to the **Insert rows** node

### 4. Configure the Nodes

#### Gmail Trigger Node
- Set the trigger condition (e.g., label, subject filter, or "has attachment")
- Recommended filter: emails with a specific subject line or label like `csv-import`

#### Extract from File Node
- Input: Binary data from Gmail attachment
- Operation: **Extract from CSV**
- This will output one item per CSV row

#### PostgreSQL Insert Node
- Select your **table name**
- Map the CSV column names to your table columns
- Enable **Continue on Fail** if you want to skip bad rows

### 5. Activate the Workflow

Toggle the workflow to **Active** in n8n. It will now listen for new Gmail messages.

---

## 🔧 Customization

- **Filter emails by subject**: Add a filter in the Gmail Trigger for specific subjects
- **Multiple tables**: Duplicate the Insert node and route rows based on CSV content
- **Error handling**: Add an error workflow in n8n to notify on failures (e.g., via Slack or email)
- **Upsert instead of insert**: Change the PostgreSQL node operation to **Upsert** to avoid duplicates

---

## 📌 Example CSV Format

Your CSV should match the schema of your PostgreSQL table. Example:

```csv
id,name,email,amount,date
1,John Doe,john@example.com,500.00,2024-01-15
2,Jane Smith,jane@example.com,750.00,2024-01-16
```

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Gmail Trigger not firing | Ensure OAuth2 credentials are valid and re-authenticate if needed |
| CSV parse error | Check that the attachment is a valid `.csv` file (not `.xlsx`) |
| DB connection failed | Verify Supabase connection string and whitelist n8n's IP in Supabase |
| Duplicate rows | Use **Upsert** operation with a unique key column |

---


## 🙌 Acknowledgements

- [n8n.io](https://n8n.io/) for the automation platform
- [Supabase](https://supabase.com/) for the managed PostgreSQL database
