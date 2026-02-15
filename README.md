# Upwork Automation Workflow (n8n)

## Project Overview

This project is a production-ready automation workflow designed to scrape, analyze, and filter Upwork job leads for an Automation Engineer. It uses a **Local LLM (Mistral)** for cost-effective AI scoring and **Mock Data** to demonstrate scraping logic without incurring API costs.

The workflow fetches job listings, pre-filters them by budget, uses AI to score their relevance, pushes qualified leads to Airtable, and routes high-priority alerts.

## Key Features

- **Mock Data Injection:** Simulates the Apify Upwork Scraper to demonstrate logic flow without API costs.
- **Resource Optimization (Pre-filtering):** Automatically discards jobs with low budgets (e.g., <$100) before sending them to the AI, saving compute resources.
- **Local AI Integration:** Uses **Ollama (Mistral model)** running locally to analyze job descriptions, assign scores (1-10), and determine priority.
- **Resilient Data Parsing:** Custom JavaScript logic uses Regex to reliably extract JSON data from the LLM, even if the model output varies.
- **Smart Routing & Alerts:**
	- **High Priority:** routed to a "Send Email" mock node.
	- **Low Priority:** routed to a logging node to prevent inbox spam.
- **Airtable Sync:** Uses an "Upsert" operation to create new records or update existing ones based on the Job Title.

## Technology Stack

- **Workflow Engine:** n8n (Self-hosted via Docker)
- **AI Model:** Mistral (via Ollama)
- **Database:** Airtable
- **Data Source:** Mock Data (Simulating Apify)

## Setup & Installation

### 1. Prerequisites

- Docker installed on your machine.
- Ollama (https://ollama.com/) installed and running.
- Airtable account.

### 2. Run n8n with Local Network Access

To allow n8n (inside Docker) to talk to Ollama (on your host machine), run the container with the `host.docker.internal` flag:

```bash
docker run -it --rm -p 5678:5678 -e N8N_HOST=localhost -e N8N_PORT=5678 -e N8N_PROTOCOL=http -v C:/n8n-data:/home/node/.n8n n8nio/n8n
```

### 3. Configure Local LLM (Ollama)

Ensure Ollama is running and pull/run the Mistral model:

```bash
ollama run mistral
```

In the workflow, the HTTP Request node should point to `http://host.docker.internal:11434/api/generate`.

### 4. Airtable Setup

Create a new Base with a table named "Upwork Leads" containing the following columns:

- Title (Single line text)
- Description (Long text)
- Score (Single line text)
- Priority (Single select: High, Medium, Low)
- Reason (Long text)
- URL (URL)
- Time Job Created (Single line text)

### 5. Import Workflow

1. Open n8n (http://localhost:5678).
2. Click "Import from File" and select `Upwork_Automation_Workflow.json`.
3. Update the Airtable Credentials in the "Create or update a record" node with your Personal Access Token.

## Usage

- Open the workflow canvas in n8n.
- Click "Execute Workflow".

Observe the flow:

1. 10 mock jobs are generated.
2. Budget Filter removes low-value jobs.
3. AI scores the remaining jobs.
4. Airtable is populated.
5. Email Alert node simulates sending notifications for "High" priority jobs.
