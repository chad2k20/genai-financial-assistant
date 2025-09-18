
# GenAI-Powered Financial Insights Assistant

# Table of Contents

1. Project Overview
2. Agent Design: Inputs, Outputs, Prompts
3. User Stories (5) — 3C format
4. Test Cases (20) — 4 per story (≥5 negative)
5. Story ↔ TestCase mapping (CSV table)
6. Architecture Diagram 
7. Deployment Guide (Azure + GitHub + Docker + CI/CD)
8. README content for repo
9. Learnings & Challenges

---

## 1) Project Overview

**Goal:** Build a GenAI-powered assistant that accepts natural-language queries, analyzes structured transaction data, and returns personalized financial summaries and actionable insights via a simple web UI.

**Core capabilities:**

* Accept transaction data (CSV/JSON) and natural-language prompt
* Parse, aggregate and analyze transactions (time-window filters, categories)
* Use a GenAI model to generate human-friendly summaries and recommendations
* Provide downloadable reports (CSV/Excel/PDF)
* Simple web UI + REST API for integration

**Non-functional requirements:**

* Privacy: transactions stay encrypted at rest and in transit
* Performance: typical query response < 3s for up to 100k transactions (analytics pre-aggregations advised)
* Extensibility: pluggable model provider (OpenAI / Azure OpenAI / local LLM)

---

## 2) Agent Design — Inputs, Outputs, Prompting

### Transaction data schema (CSV / JSON)

* transaction\_id: string
* date: YYYY-MM-DD
* amount: float (negative for debit/expense, positive for credit)
* currency: string (e.g., INR)
* merchant: string
* category: string (groceries, dining, utilities, etc.)
* account\_id: string
* notes: string (optional)

### NLP prompt inputs (from Web UI / API)

* user\_id
* natural\_language\_query (e.g., "How much did I spend on groceries last month?")
* time\_window (optional): { from: YYYY-MM-DD, to: YYYY-MM-DD }
* model\_config: { provider: "azure\_openai" | "openai", model: "gpt-4o" | ... }
* attach\_transaction\_sample (optional): boolean

### Agent internal pipeline

1. **Parse** the query (intent extraction: metric, category, time range, comparison requests)
2. **Fetch / Filter** transaction data according to parsed constraints
3. **Aggregate / Analyze**: sums, counts, trends, percent change vs previous window, outliers
4. **Prepare context**: create a succinct data summary (max 800 tokens) for the GenAI model — e.g., aggregated numbers and small bullet lists of top merchants
5. **Generate**: call GenAI with a system prompt + user prompt + structured context. Ask for JSON response conforming to schema
6. **Post-process**: validate JSON, attach charts or CSV if requested
7. **Respond**: return natural language summary + structured insights + downloadable attachments

### Recommended model prompt pattern (system + user)

**System**: "You are a helpful financial insights assistant. The user will provide aggregated transaction stats and a question. Return a concise summary (2–5 sentences), five actionable insights, and a JSON object with fields: metric, value, unit, period, trend, suggestions\[]. Keep privacy: do not reveal raw PII."

**User**: includes: original query + the aggregated context block.

### Expected structured response (JSON)

```json
{
  "summary": "Spent ₹12,400 on groceries in August 2025, a 8% increase vs July.",
  "metric": "groceries_spend",
  "value": 12400.00,
  "unit": "INR",
  "period": {"from":"2025-08-01","to":"2025-08-31"},
  "trend": "up",
  "trend_percent": 8.0,
  "top_merchants": [
    {"merchant":"BigMart","amount":4200.00},
    {"merchant":"GreenGrocer","amount":3100.00}
  ],
  "insights": ["Consider switching to weekly bulk buys","Set a grocery budget of ₹10,000 for next month"],
  "actions": [
    {"type":"suggestion","text":"Create a recurring rule to move ₹2,000/month to ‘Groceries Savings’"}
  ]
}
```

---

## 3) User Stories (5) — Card, Conversation, Confirmation (3C)

User Story 1 — Customer: Spending Summary

Card: As a customer, I want to ask “How much did I spend on groceries last month?” so that I can track my budget.

Conversation:
User enters a natural query in plain English.
System fetches transactions categorized under groceries.
GenAI agent summarizes spending.

Confirmation:
The system returns total grocery spending for the requested month.

User Story 2 — Customer: Savings Suggestions

Card: As a customer, I want to receive saving tips based on my spending patterns so that I can reduce unnecessary expenses.

Conversation:
System analyzes recurring expenses and categories.
GenAI agent generates personalized tips.

Confirmation:
At least 2 actionable suggestions are displayed (e.g., reduce dining out, use offers).


User Story 3 — Financial Analyst: Trend Analysis

Card: As a financial analyst, I want to view a trend of monthly spending across categories so that I can identify overspending behavior.

Conversation:
Analyst inputs a request for trend analysis.
System aggregates category-wise spending over 6 months.
GenAI agent provides insights.

Confirmation:
Line chart or table is generated with at least 3 months’ comparison.

User Story 4 — Developer: Data Export

Card: As a developer, I want to export financial insights to CSV/PDF so that they can be shared with other systems or stakeholders.

Conversation:
User clicks “Export Report.”
System packages insights into CSV or PDF.

Confirmation:
The file downloads successfully and contains the insights shown on screen.

User Story 5 — Customer: Budget Alerts

Card: As a customer, I want to get alerts when I exceed my monthly budget so that I can control my expenses.

Conversation:
Customer sets a budget limit.
System monitors transactions in real time.
Webhook/alert triggers when limit exceeded.

Confirmation:
User receives notification (email, SMS, or in-app).

---

## 4) Test Cases — 4 per story (20 total)

> Each test case includes: Test ID, Description, Preconditions, Test Steps, Expected Result, Actual Result (left blank)

### Story 1 — Monthly Category Summary

**TC1.1**

* **Description:** Verify correct total for groceries in a given month
* **Preconditions:** Valid transaction CSV uploaded with categorized groceries transactions for August 2025
* **Steps:** 1) Query: "How much did I spend on groceries in August 2025?" 2) Wait for response
* **Expected Result:** System returns total equal to aggregated sum of groceries for Aug 2025, with percent change vs July
* **Actual Result:**

**TC1.2**

* **Description:** Verify time-window parsing when user omits exact month wording
* **Preconditions:** Transactions exist for last 30 days and for last calendar month
* **Steps:** 1) Query: "How much did I spend on groceries last month?" 2) Confirm system asks "calendar month or last 30 days?" 3) Select calendar month
* **Expected Result:** System clarifies then returns correct calendar-month total
* **Actual Result:**

**TC1.3 (Negative)**

* **Description:** Handle missing category labels gracefully
* **Preconditions:** Transactions uploaded but some grocery transactions are uncategorized
* **Steps:** 1) Query for groceries for period 2) Observe response
* **Expected Result:** System returns sum of transactions tagged as groceries and states number of uncategorized transactions and offers to auto-categorize
* **Actual Result:**

**TC1.4**

* **Description:** Verify top merchants list accuracy
* **Preconditions:** Transactions include multiple merchants with known amounts
* **Steps:** 1) Query for groceries month 2) Check top merchants list in response
* **Expected Result:** Merchants are sorted by spend amount and top 3 are correct
* **Actual Result:**

---

### Story 2 — Trend & Alerts

**TC2.1**

* **Description:** Create an alert for >20% increase in dining spend
* **Preconditions:** User account exists and has historical dining spend for comparison
* **Steps:** 1) Set alert via UI or query 2) Simulate a month with >20% increase
* **Expected Result:** Alert triggers and sends notification with supporting transactions
* **Actual Result:**

**TC2.2**

* **Description:** Verify alert delivery channels
* **Preconditions:** User has email and in-app notification settings configured
* **Steps:** 1) Create alert 2) Trigger alert 3) Confirm email and in-app notification
* **Expected Result:** Notifications received on both channels according to preferences
* **Actual Result:**

**TC2.3 (Negative)**

* **Description:** Invalid threshold value should be rejected
* **Preconditions:** User attempts to set a threshold of -10%
* **Steps:** 1) Create alert with -10% threshold 2) Observe system validation
* **Expected Result:** System rejects invalid threshold and explains valid range (e.g., 1–100%)
* **Actual Result:**

**TC2.4**

* **Description:** Verify alert message contains delta and sample transactions
* **Preconditions:** Alert triggered
* **Steps:** 1) Trigger alert 2) Inspect alert content
* **Expected Result:** Alert contains percent change, previous month amount, current amount, and 3 sample transactions
* **Actual Result:**

---

### Story 3 — Export & Reproducible Report

**TC3.1**

* **Description:** Generate PDF report with category breakdown
* **Preconditions:** Analyst selects Jan–Mar 2025 and requests PDF
* **Steps:** 1) Request report 2) Download PDF 3) Open and verify contents
* **Expected Result:** PDF contains category breakdown, charts and top 10 merchants, and matches CSV numbers
* **Actual Result:**

**TC3.2**

* **Description:** CSV export matches internal aggregates
* **Preconditions:** Report generated
* **Steps:** 1) Download CSV 2) Re-aggregate locally 3) Compare to displayed aggregates
* **Expected Result:** Numbers match exactly
* **Actual Result:**

**TC3.3 (Negative)**

* **Description:** Request report for a time range with no data
* **Preconditions:** Date range with zero transactions
* **Steps:** 1) Request report 2) Observe response
* **Expected Result:** System returns "No data for selected range" and offers adjacent ranges or sample accounts
* **Actual Result:**

**TC3.4**

* **Description:** Verify report generation performance
* **Preconditions:** Dataset of 100k transactions
* **Steps:** 1) Request PDF with charts 2) Measure time to generate
* **Expected Result:** Report generated in acceptable time (e.g., <30s in staging). Document performance in logs.
* **Actual Result:**

---

### Story 4 — Model Provider Swap

**TC4.1**

* **Description:** Swap provider to Azure OpenAI and run smoke test
* **Preconditions:** Azure credentials configured in CI/staging
* **Steps:** 1) Change config to Azure 2) Deploy to staging 3) Run sample query
* **Expected Result:** Model responds and returns valid JSON structure
* **Actual Result:**

**TC4.2**

* **Description:** Verify fallback when provider key invalid
* **Preconditions:** Invalid API key configured
* **Steps:** 1) Deploy with invalid key 2) Run smoke test
* **Expected Result:** System fails gracefully, logs error, and falls back to previously configured provider or returns a friendly error
* **Actual Result:**

**TC4.3 (Negative)**

* **Description:** Model output missing required JSON fields
* **Preconditions:** Model returns free-form text
* **Steps:** 1) Simulate model hallucination by sending ambiguous context 2) Inspect response
* **Expected Result:** Validation layer catches missing fields and retries with a stricter prompt or returns an error to the user
* **Actual Result:**

**TC4.4**

* **Description:** Verify model provider config is stored securely
* **Preconditions:** Configuration stored in Key Vault / secret store
* **Steps:** 1) Inspect deployment for secret usage 2) Attempt to read provider key from app logs
* **Expected Result:** Keys are not present in logs; secrets are read from secure store
* **Actual Result:**

---

### Story 5 — Savings Recommendation & Rule Creation

**TC5.1**

* **Description:** Generate 3 personalized saving actions
* **Preconditions:** User has >3 months of transactions
* **Steps:** 1) Query: "Help me save on groceries" 2) Inspect suggestions
* **Expected Result:** Agent returns 3 actionable, prioritized suggestions with estimated impact
* **Actual Result:**

**TC5.2**

* **Description:** Create auto-transfer rule from suggestions
* **Preconditions:** Payment linked and rules API available
* **Steps:** 1) Approve suggested action 2) Confirm rule creation 3) Inspect scheduled jobs
* **Expected Result:** Rule created and next run scheduled
* **Actual Result:**

**TC5.3 (Negative)**

* **Description:** Attempt rule creation when payment method missing
* **Preconditions:** No linked payment method
* **Steps:** 1) Approve auto-transfer suggestion 2) Observe system behavior
* **Expected Result:** System rejects action, prompts user to add a payment method
* **Actual Result:**

**TC5.4**

* **Description:** Verify suggested impact estimation accuracy
* **Preconditions:** Baseline monthly spend known
* **Steps:** 1) Request savings suggestion 2) Calculate expected impact from suggestion and compare to agent's estimation
* **Expected Result:** Estimated savings should be consistent with computed values within 5% margin
* **Actual Result:**

---

**Notes on negative cases:** TC1.3, TC2.3, TC3.3, TC4.3, TC5.3 are the 5 negative test cases (≥25%).

---

## 5) Story ↔ TestCase Mapping (CSV)

```csv
story_id,story_title,test_id
S1,Monthly Category Summary,TC1.1
S1,Monthly Category Summary,TC1.2
S1,Monthly Category Summary,TC1.3
S1,Monthly Category Summary,TC1.4
S2,Merchant Drilldown,TC2.1
S2,Merchant Drilldown,TC2.2
S2,Merchant Drilldown,TC2.3
S2,Merchant Drilldown,TC2.4
S3,Budget Recommendations,TC3.1
S3,Budget Recommendations,TC3.2
S3,Budget Recommendations,TC3.3
S3,Budget Recommendations,TC3.4
S4,API & Webhook Integration,TC4.1
S4,API & Webhook Integration,TC4.2
S4,API & Webhook Integration,TC4.3
S4,API & Webhook Integration,TC4.4
S5,Compliance & Data Export,TC5.1
S5,Compliance & Data Export,TC5.2
S5,Compliance & Data Export,TC5.3
S5,Compliance & Data Export,TC5.4
```

> You can copy the CSV into a spreadsheet to create the mapping sheet. Consider adding columns later: priority, owner, estimate, status.



# 7) Deployment Guide (Azure + GitHub + Docker + CI/CD)

# Repo layout (suggested)

```
/
  README.md
  architecture.png (exported from mermaid)
  /docs
    user_stories.md
    tests.md
    deploy.md
  /backend
    Dockerfile
    app (node/python/fastapi)
  /frontend
    Dockerfile
    react-app
  /infra
    azure-deploy.yml (ARM/Bicep/Terraform)
  /ci
    .github/workflows/ci-cd.yml
```

# 8) Learnings & Challenges

* Prompt engineering:** Constrain model output to strict JSON to avoid hallucinations; use a validation / schema enforcement layer
* Data privacy:** Minimize what goes to the model — prefer aggregated stats instead of raw transactions
* Scalability:** Pre-aggregation for common queries (daily/monthly/category) reduces model context size and latency
* Testing LLM outputs:** Create tests that validate schema, numeric accuracy, and plausibility rather than exact text
* CI/CD complexities:** Rotating secrets and multi-provider deployments require robust secret management and staging environments

---

# Appendices

* **Appendix A:** Sample API endpoints

  * `POST /api/query` — body: { user\_id, query, time\_window } — returns JSON result + attachments
  * `POST /api/upload` — multipart CSV upload
  * `GET /api/report/{report_id}` — returns PDF/CSV

* **Appendix B:** Example prompt (full)

```
System: You are a financial-assistant that must only return JSON in the following schema: {summary, metric, value, unit, period, trend, trend_percent, top_merchants[], insights[], actions[]}.
User: Question: "How much did I spend on groceries in August 2025?"; Context: {total_groceries: 12400, prev_total: 11500, top_merchants: [{"BigMart":4200}, {"GreenGrocer":3100}]}
```

---

*End of document.*

