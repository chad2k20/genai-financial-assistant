
# GenAI-Powered Financial Insights Assistant

## 📌 Overview
This project is a prototype **GenAI-powered assistant** that provides personalized financial insights based on transaction data and natural language queries.

### ✨ Features
- Accepts **natural language prompts** (e.g., "How much did I spend on groceries last month?")
- Analyzes structured **transaction data** (CSV/DB)
- Uses **GenAI (e.g., Azure OpenAI)** for summaries, insights, saving tips
- Provides actionable **financial insights**
- Exposes **API** endpoint (`/api/insights`)
- Deployable to **Azure Cloud** (via Docker + GitHub Actions)

---

## 📂 Project Structure
/ (repo root)
├─ README.md
├─ .gitignore
├─ docs/
│ ├─ user_stories.md
│ ├─ tests.md
│ ├─ deploy.md
│ └─ architecture.png
├─ data/
│ └─ sample_transactions.csv
├─ api/
│ ├─ app.py
│ ├─ requirements.txt
│ └─ Dockerfile
├─ web/ (React frontend - optional)
├─ infra/ (deployment scripts & CI/CD)
└─ tests/ (unit + integration tests)



