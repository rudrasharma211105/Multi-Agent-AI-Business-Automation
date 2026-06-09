# Multi-Agent-AI-Business-Automation

An enterprise-grade, decentralized automation ecosystem built on **n8n**. This suite utilizes an **Orchestrator-Worker** architecture powered by **Google Gemini LLM** to triage, process, and route inbound multi-channel data (Emails, Invoices, Webhooks, Form Submissions) across five core business functions.

---

## 🏗️ Core Architecture Overview

The system is split into two layers:
1. **The Ingestion & Orchestration Layer**: Captures data via multi-trigger webhooks and dynamically routes payloads to specialized departmental sub-workflows using AI-driven classification tools.
2. **The Execution & Worker Layer**: Specialized standalone workflows that perform narrow, department-specific automation (CRM logging, calendar management, generative image asset creation, automated contextual drafting).

---

## 📂 Workflow Blueprint Breakdowns

### 1. Ingestion Gateways (`WebHook`)
* **Purpose:** Serves as the central API gateway and listener for all external events.
* **Key Components:**
  * **Gmail Trigger:** Evaluates real-time incoming inbox metadata.
  * **Conditional Traversal (If Node):** Separates native email schemas from custom structure webhooks.
  * **Departmental Webhooks (HR, Invoice, Webhook):** Provides dedicated, static JSON listener endpoints.
  * **HTTP Request Dispatchers:** Standardizes incoming JSON payloads and dispatches them via secure `POST` streams to the master Orchestrator endpoint (`http://localhost:5678/...`).

### 2. Central Router (`Multi_Business_Orchestrator`)
* **Purpose:** The cognitive switchboard of the ecosystem. It ingests unsorted payloads and executes zero-shot classification to choose the downstream business unit.
* **Key Components:**
  * **Webhook Node:** Captures normalized payloads from the ingestion gateway.
  * **Extract from File (PDF Parser):** Converts unstructured binary streams (e.g., invoice attachments, resumes) into raw, readable text.
  * **AI Agent (Router):** Uses a **Google Gemini Chat Model** and explicit structural guidelines to determine context, urgency, and destination department.
  * **Sub-Workflow Tool Integration:** Dynamically acts as a dispatcher calling target worker workflows (`Customer_Support`, `HR`, `Sales/Leads`, `Invoice`, `Marketing`) based on classification indices.

### 3. Customer Support Workflow (`Customer_Support`)
* **Purpose:** Evaluates inbound support tickets, flags user sentiment, handles multi-tier message forwarding, and manages escalation tables.
* **Routing Matrix (Switch Node):**
  * `URGENT_CUSTOMER_ISSUE`: Escalates a fast-track critical Gmail notification, updates priority sheets, and triggers high-visibility **Slack alerts**.
  * `PAYMENT_ISSUES`: Routes financial queries directly to account specialists via automated mail templates and logging arrays.
  * `PRODUCT_HELP` / `FEATURE_REQUEST`: Intercepts tickets using a dedicated **Gemini Model Refinement Agent** to generate immediate, context-aware troubleshooting responses, and stores metadata in technical backlogs.
  * `SPAM`: Blocks downstream executions and routes addresses into a central audit table.

### 4. Human Resources & Recruiting (`HR`)
* **Purpose:** Standardizes applicant tracking pipelines, controls recruitment scheduling, and automates applicant notifications.
* **Routing Matrix (Switch Node):**
  * `SHORTLIST`: Schedules interview blocks natively via **Google Calendar (Create an event)**, drafts confirmation briefs to candidates via Gmail, and writes applicant details into a master evaluation sheet.
  * `HOLD` / `REJECT`: Triggers structured personal notifications via Gmail and updates active pipeline databases with custom tags.

### 5. Sales & Lead Pipeline Management (`Sales/Leads`)
* **Purpose:** Processes incoming leads, performs intent scoring, maps prospect records to operational sheets, and alerts field agents.
* **Routing Matrix (Switch Node):**
  * `READY_TO_BUY` / `NEEDS_CALL`: Logs contact payloads to primary CRMs, sends immediate response confirmations, and issues synchronized team calendar tasks.
  * `PARTNERSHIP`: Fires customized strategic B2B templates, aggregates metadata into a partner CRM table, and posts tracking summaries to core Slack channels.

### 6. Invoice Processing Pipeline (`Invoice`)
* **Purpose:** Processes vendor invoice payloads, runs structural health audits, and logs balance records.
* **Routing Matrix (Switch Node):**
  * `APPROVED`: Dispatches validation receipts and appends line-item financial data to centralized ledger sheets.
  * `SUSPICIOUS` / `REVIEW_REQUIRED`: Pauses downstream automation, marks records as high-risk, logs them into an escalation database, and alerts compliance teams.

### 7. Marketing & Content Generation Engine (`Marketing`)
* **Purpose:** Listens for brand content prompts and coordinates multi-channel copywriting and media generation.
* **Routing Matrix (Switch Node):**
  * `EMAIL`: Orchestrates an isolated sub-agent loop (`AI Agent1`) to construct targeted newsletters, draft outbound marketing campaigns, and keep distribution sheets up to date.
  * `SOCIAL_MEDIA`: Calls **Imagen Generative AI Tools** to programmatically compile marketing imagery alongside contextually matching ad-copy text, and publishes the combined assets directly to social platforms via the **Facebook Graph API**.

---

## 🛠️ Global Component Requirements

* **Orchestration Platform:** n8n (v1.0.0+ recommended)
* **LLM Engine & Architecture:** Google Gemini Chat Node + Structured Output Parser
* **Core Integrations:**
  * Google Workspace (Gmail, Google Sheets, Google Calendar)
  * Messaging Systems (Slack API)
  * Paid Media Networks (Facebook Graph API)
  * Media Utilities (Imagen Generator Node, PDF Text Extraction Tool)

---

## 🚀 Setup & Installation

1. **Clone the Core Repository:**
   ```bash
   git clone [https://github.com/your-organization/n8n-multi-business-suite.git](https://github.com/your-organization/n8n-multi-business-suite.git)
   cd n8n-multi-business-suite
Configure Platform Environment Variables (.env):

Code snippet
N8N_ENCRYPTION_KEY=your_secure_key
GEMINI_API_KEY=your_google_gemini_api_token
SLACK_BOT_TOKEN=xoxb-your-slack-token
FACEBOOK_ACCESS_TOKEN=your_facebook_graph_token
Import System Blueprints:

Open your n8n workspace panel.

Select Workflows > Import from File for each workflow design (Webhook.json, Orchestrator.json, etc.).

Link your Google Service Account, Slack, and Facebook credentials to the respective node instances.

Activate & Test:

Turn the toggle switch to Active on all imported worker workflows.

Toggle the WebHook ingestion workflow to Active.

Run a test mock payload against the central WebHook endpoint to ensure the Orchestrator routes the item down the proper operational channel.