# Upwork Automation Project Report

**Submitted by:** Harsh Gupta
**Date:** February 8, 2026

## 1. Executive Summary
This project successfully deploys an end-to-end automation workflow for scoring Upwork job leads. To demonstrate logic capabilities while maintaining zero cost, the project utilizes **Mock Data** (simulating Apify) and a **Local LLM (Mistral)**. The workflow features advanced logic including batch processing, budget pre-filtering, and conditional routing for alerts.

## 2. Issues Identified & Fixed during Development

| Issue | Description | Solution Implemented |
| :--- | :--- | :--- |
| **"PairedItem" Data Loss** | The `Output Function` node initially processed only the first item, causing subsequent nodes to lose reference to the original job data (Title, URL). | **Fixed:** Refactored the JavaScript code to use `.map()` on `$input.all()`, ensuring all incoming items are processed and correctly paired with their original data. |
| **LLM JSON Parsing** | The Local LLM (Mistral) occasionally returned conversational text alongside the requested JSON, breaking the flow. | **Fixed:** Implemented Regex extraction (`text.match`) in the Code Node to reliably pull out the "Score" and "Priority" regardless of the surrounding text. |
| **API Cost Constraints** | The assignment requested paid APIs (Apify/OpenAI), but budget was a constraint. | **Fixed:** Utilized instructor-approved alternatives: Mock Data node for sourcing and Ollama (Mistral) for intelligence. |
| **Resource Waste** | AI was processing low-value jobs (e.g., $50 budget), wasting compute time. | **Fixed:** Added a "Pre-filtering" node to discard jobs with budgets under $100 *before* the AI call. |

## 3. Sample Scored Jobs (From Execution)

Below are the five actual jobs used in the run, with the AI's score, priority, and short reasoning for each.

**1. Title:** n8n Automation Expert Needed for CRM Integration
- **Score:** 5/10
- **Priority:** High
- **Reasoning:**  - The job posting is highly relevant to automation as it requires an expert to build complex workflows using n8n, a powerful open-source tool for workflow automation.

**2. Title:** Graphic Designer for Logo Creation
- **Score:** 3/10
- **Priority:** Low
- **Reasoning:** While graphic design tasks can be automated to some extent using AI tools, the specific requirements of this job - a modern minimalist logo for a coffee shop - are best suited for human designers with their creative abilities and understanding of brand identity. Adobe Illustrator is not an automation-friendly tool in this context as it requires intricate manual design work. However, if AI were to be used for this task, it would likely involve using AI tools to refine or suggest ideas rather than completing the entire logo creation process.

**3. Title:** Python Script for Web Scraping Real Estate Data
- **Score:** 7/10
- **Priority:** Medium
- **Reasoning:**  - The job involves using Python and libraries like BeautifulSoup or Selenium for web scraping, which are commonly used for automation tasks. However, the specific focus is on real estate data from Zillow, which might require some additional steps to navigate their website structure and extract desired data.

**4. Title:** Build a Custom API Connector for n8n
- **Score:** 8/10
- **Priority:** High
- **Reasoning:**  - This job involves the development of a custom node for n8n to interact with an API, which is directly related to automation as it deals with integrating different software systems. The use of Node.js is also a good sign that the job requires programming skills, which are crucial in building custom connectors.

**5. Title:** SEO Content Writer for Tech Blog
- **Score:** 7/10
- **Priority:** Medium
- **Reasoning:**  - This job requires an intermediate SEO Content Writer to produce articles about the latest trends in AI and Machine Learning. While automation can certainly help generate content, it is less likely to be effective in this case due to the need for understanding and interpreting the latest trends in a rapidly evolving field. Human writers are better suited for this task because they have the ability to research and analyze new developments, understand the nuances of the subject matter, and write engaging and informative content that is optimized for SEO. However, automation tools can be used as a supplement to aid in keyword research and SEO optimization.

## 4. Suggested Future Improvements

To move this from a "Production-Ready Demo" to a live commercial tool, I recommend:

1.  **Live Data Source:** Replace the Mock Data node with the **Apify Upwork Scraper** once a budget is allocated. The logic downstream will remain identical.
2.  **Feedback Loop:** Add a "User Rating" field in Airtable. If the user marks a "High Priority" job as "Bad," feed that data back into the prompt to refine future scoring.
3.  **Cloud LLM Fallback:** Implement a switch to use **OpenAI GPT-4o** for complex reasoning if the Local LLM is unsure (e.g., if the confidence score is low).
4.  **Real-Time Alerting:** Connect the "Email Alert" node to the **Gmail API** to send actual emails for High Priority leads.
