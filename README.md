
# GenAI-Powered Financial Insights Assistant

Table of Contents

1.Project Overview
2.Agent Design: Inputs, Outputs, Prompts
3.User Stories (5) — 3C format
4.Test Cases (20) — 4 per story (≥5 negative)
5.Story ↔ TestCase mapping (CSV table)
6.Architecture Diagram (Mermaid)
7.Deployment Guide (Azure + GitHub + Docker + CI/CD)
8.README content for repo
9.Learnings & Challenges

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


01_finance_assistant/
├── data/                         # Data-related files
│   ├── README.md                 # Documentation for data directory
│   └── schema.sql                # SQL schema for database initialization
│
├── models/                       # Model implementations
│   ├── README.md                 # Documentation for models directory
│   ├── categorizer.py            # Transaction categorization logic
│   ├── forecaster.py             # Spending forecasting model using time series analysis
│   ├── llm_assistant.py          # Ollama LLM integration for AI-powered chat
│   └── recommender.py            # Financial recommendation engine
│
├── tests/                        # Test files
│   ├── README.md                 # Documentation for tests directory
│   └── test_forecaster.py        # Tests for the forecasting model
│
├── ui/                           # Streamlit user interface
│   ├── README.md                 # Documentation for UI directory
│   ├── finance_app.py            # Enhanced Streamlit application with chat feature
│   ├── legacy/                   # Older versions of the UI
│   │   ├── README.md             # Documentation for legacy UI files
│   │   ├── app.py                # Original Streamlit application
│   │   ├── basic_app.py          # Simplified version of the UI
│   │   └── simple_app.py         # Minimal implementation of the UI
│   └── sample_data/              # Sample CSV files for testing
│       ├── README.md             # Documentation for sample data files
│       ├── bank_statement_sample.csv # Bank statement format
│       ├── credit_card_statement.csv # Credit card statement format
│       ├── european_format.csv   # European format with semicolons
│       ├── sample_transactions.csv # Basic sample transactions
│       ├── simple_transactions.csv # Simple format for testing
│       └── very_simple.csv       # Minimal example for testing
│
├── utils/                        # Utility functions and helpers
│   ├── README.md                 # Documentation for utils directory
│   ├── data_processor.py         # Data processing utilities
│   └── visualizations.py         # Visualization utilities
│
├── app.py                        # Command-line application for initialization
├── db_init.py                    # Database initialization module
├── finance.db                    # SQLite database for storing financial data
├── requirements.txt              # Project dependencies
├── run.py                        # Script to run the application with automatic setup
├── run_streamlit.py              # Script to run the Streamlit UI
├── test_ollama.py                # Script to test Ollama LLM connectivity
├── .gitignore                    # Git ignore file
└── README.md                     # Project documentation



