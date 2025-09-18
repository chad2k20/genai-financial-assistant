# 📖 User Stories – GenAI Financial Insights Assistant  

This file contains **5 Agile user stories** written in the **Card, Conversation, Confirmation** (3C) format, aligned with the `story_test_mapping_detailed.xlsx`.  

---

## Story 1 – Monthly Spending Summary (S1)  
**Card**  
As a **customer**, I want to know how much I spent on groceries last month so that I can track my monthly budget.  

**Conversation**  
- Customer: “How much did I spend on groceries last month?”  
- Assistant: “You spent ₹8,450 on groceries last month.”  
- Customer: “Show me the trend for the last 2 months.”  
- Assistant: “July: ₹7,200, August: ₹8,450 (+17%).”  

**Confirmation**  
- Assistant retrieves grocery transactions correctly.  
- Shows both totals and trend insights across months.  
- If no grocery data exists, responds with “No data available.”  
- Invalid categories (e.g., dragons) return “Category not found.”  

---

## Story 2 – Merchant Drilldown (S2)  
**Card**  
As a **customer**, I want to drill down into my spending by merchant so that I can understand where my money goes.  

**Conversation**  
- Customer: “Show my spending on Amazon.”  
- Assistant: “You spent ₹5,000 on Amazon in August.”  
- Customer: “What about Amazon and Flipkart combined?”  
- Assistant: “Amazon: ₹5,000, Flipkart: ₹3,200.”  
- Customer: “What about BestBuy?”  
- Assistant: “No transactions found for BestBuy.”  

**Confirmation**  
- Assistant provides correct totals per merchant.  
- Handles multiple merchants in one query.  
- Returns clear error if merchant not found.  
- Gracefully rejects malformed queries.  

---

## Story 3 – Budget Recommendations (S3)  
**Card**  
As a **customer**, I want budget recommendations based on my past spending so that I can manage overspending.  

**Conversation**  
- Customer: “Suggest a budget for groceries.”  
- Assistant: “You overspent by 20% last month. Suggested monthly grocery budget: ₹7,000. Tip: buy in bulk to save costs.”  
- Customer: “What about utilities?”  
- Assistant: “Your spending on utilities is stable, no major change. Current budget is sufficient.”  

**Confirmation**  
- Assistant detects overspending trends.  
- Suggests realistic budget adjustments.  
- Provides actionable saving tips.  
- If no overspending, confirms stability.  

---

## Story 4 – Saving Insights (S4)  
**Card**  
As a **customer**, I want to receive personalized saving insights so that I can improve my financial health.  

**Conversation**  
- Customer: “Give me saving tips for entertainment.”  
- Assistant: “You spent ₹6,100 on entertainment in September, up 20% from August. Suggestion: set a cap of ₹4,500 and use free alternatives.”  
- Customer: “Any tips for groceries?”  
- Assistant: “Consider meal planning. This could reduce grocery spending by 10%.”  

**Confirmation**  
- Assistant analyzes trends before suggesting savings.  
- Provides actionable, category-specific insights.  
- Tips are contextual (not generic).  
- If data is missing, responds with “No sufficient data for suggestions.”  

---

## Story 5 – Data Export & Reporting (S5)  
**Card**  
As a **financial analyst**, I want to export categorized summaries to Excel so that I can prepare reports for clients.  

**Conversation**  
- Analyst: “Summarize my September spending by category.”  
- Assistant: “Groceries: ₹8,450 | Rent: ₹12,000 | Entertainment: ₹6,100 | Utilities: ₹3,500 | Transport: ₹2,200.”  
- Analyst: “Export to Excel.”  
- Assistant: “Generated file: `September_Spending_Summary.xlsx`.”  

**Confirmation**  
- Assistant categorizes transactions correctly.  
- Shows totals per category.  
- Provides export in Excel/CSV format.  
- File is generated with correct structure and values.  
