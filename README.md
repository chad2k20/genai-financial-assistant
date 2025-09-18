
# GenAI-Powered Financial Insights Assistant

# Table of Contents

1. Project Overview
2. Problem Statement
3. Features
4. Repository Structure
5. Architecture
6. Agile Deliverables
7. Deployment Plan
8. Learnings & Challenges

---

## 1) Project Overview

This project designs a GenAI-powered financial assistant that takes transaction data and natural language
prompts as input, and generates:

Personalized financial insights (spending summaries, saving tips, trends)
Agile user stories in 3C format
Comprehensive test cases
A traceability spreadsheet mapping user stories to test cases

The focus is on Agile design, testing, and deployment planning, with documentation and artifacts prepared for cloud deployment.

## 2)Problem Statement

Users have structured financial transaction data but don’t want to manually analyze it. Instead, they want to ask questions like:

“How much did I spend on groceries last month?”

The assistant interprets these prompts, processes transaction data, and returns clear insights.
---

## 3)Features

✔ Accepts transaction data + NLP prompt
✔ Generates personalized insights
✔ Auto-creates user stories & test cases
✔ Provides a spreadsheet mapping for traceability
✔ Cloud deployment plan with Azure + GitHub + Docker + CI/CD (optional)

## 4)📂 Repository Structure

GenAI-Financial-Insights-Assistant/
│
├── README.md                 # Project overview (this file)
│
├── docs/                     # Documentation
│   ├── user_stories.md        # 5 Agile user stories (3C format)
│   ├── tests.md               # 20 test cases (positive + negative)
│   ├── story_testcase_mapping.xlsx  # Story ↔ test case mapping spreadsheet
│   ├── architecture.png       # System architecture diagram
│   ├── deploy.md              # Deployment guide (Azure, GitHub, Docker)
│   └── learnings.md           # (Optional) Challenges, learnings, future work
│
└── data/                      # Sample input data
    ├── transactions.xlsx      # Example structured transaction data
    └── sample_prompts.txt     # Example natural language queries

## 5)🧩 Architecture

1.Input → Transaction Data + NLP Prompt
2.Processing → GenAI Agent (NLP + Data Analysis)
3.Output →
  Financial Insights
  User Stories
  Test Cases + Mapping
  
## 6)✅ Agile Deliverables

1. User Stories (docs/user_stories.md)
    5 user stories written in Card, Conversation, Confirmation format.
    Roles covered: Customer, Developer, Analyst.

2. Test Cases (docs/tests.md)
    20 test cases (≥25% negative).
    Each case includes: Test ID, Description, Preconditions, Steps, Expected Result.

3. Traceability Matrix (docs/story_testcase_mapping.xlsx)
    Links each User Story ↔ Test Cases.
    Ensures complete coverage.

## 7)🚀 Deployment Plan (docs/deploy.md)

Cloud Platform: Azure
Version Control: GitHub
Optional: Docker for containerization, CI/CD pipelines for automation
Steps:
  1.Setup Azure environment
  2.Push repo to GitHub
  3.Configure build & release pipelines
  4.Deploy containerized assistant

## 8)📚 Learnings & Challenges (docs/learnings.md)

1.Designing user stories for GenAI assistants
2.Handling negative test cases (invalid prompts, missing data)
3.Mapping business requirements to test cases
4.Planning deployment strategy before coding


