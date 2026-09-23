# 🤖 SQL Database AI Agent

A practical **Generative AI / Agentic AI project** that allows users to ask questions about a SQL database using natural language.

The system uses an **LLM to generate SQL**, validates the generated query, executes it against a SQLite database, and then converts the database result into a clear natural-language response.

> 🚀 **Natural Language → SQL → Database → Result → AI Explanation**

---

## 📌 Project Overview

Traditional database systems require users to know SQL before they can retrieve information.

For example, a traditional SQL workflow might require:

```sql
SELECT c.name, COUNT(o.order_id) AS order_count
FROM customers c
JOIN orders o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name
ORDER BY order_count DESC;
```

With this AI Agent, the user can simply ask:

```text
"Which customer placed the most orders?"
```

The LLM understands the question, generates an appropriate SQL query, executes it against the database, and explains the result.

---

# 🎯 Objectives

This project demonstrates how to build a **GenAI-powered SQL Database Agent**.

The main objectives are:

* 🤖 Build a natural-language database assistant
* 🧠 Use an LLM for Text-to-SQL generation
* 🔎 Provide database schema context to the LLM
* 📝 Generate SQL automatically
* 🛡️ Validate generated SQL before execution
* 🗄️ Execute SQL against a relational database
* 📊 Process database results
* 💬 Generate natural-language answers
* 🔐 Restrict the agent to read-only operations
* 🧪 Test the system with multiple questions

---

# 🏗️ Architecture

The project follows this architecture:

```text
                    👤 User
                      │
                      │ Natural Language
                      ▼
                🧠 LLM Agent
                      │
                      │ Database Schema
                      ▼
               📝 SQL Generation
                      │
                      ▼
                🛡️ SQL Validator
                      │
             ┌────────┴────────┐
             │                 │
          ❌ Unsafe          ✅ Safe
             │                 │
             ▼                 ▼
          Reject          🗄️ SQLite
                               │
                               ▼
                         📊 Query Result
                               │
                               ▼
                          🧠 LLM
                               │
                               ▼
                        💬 Final Answer
```

---

# 🔥 Example

### User Question

```text
Which customers are from Chennai?
```

### AI-generated SQL

The LLM may generate:

```sql
SELECT name, city
FROM customers
WHERE city = 'Chennai';
```

### Database Result

```text
Arun       Chennai
Divya      Chennai
```

### AI Response

```text
There are two customers from Chennai: Arun and Divya.
```

---

# 🧠 Core GenAI Concepts

This project demonstrates several important Generative AI concepts.

## 1. 📝 Text-to-SQL

The LLM converts natural-language questions into SQL.

```text
"What products are in the Accessories category?"
```

↓

```sql
SELECT product_name
FROM products
WHERE category = 'Accessories';
```

---

## 2. 🔎 Schema-Aware Generation

The LLM receives information about the available database tables and columns.

Example:

```text
customers
 ├── customer_id
 ├── name
 ├── city
 └── segment

products
 ├── product_id
 ├── product_name
 ├── category
 └── price

orders
 ├── order_id
 ├── customer_id
 ├── product_id
 ├── quantity
 └── order_date
```

This allows the model to generate SQL based on the actual database structure.

---

# 🗄️ Database

The notebook creates a sample SQLite database:

```text
sales_ai_agent.db
```

It contains three tables.

### 👥 Customers

```text
customer_id
name
city
segment
```

### 📦 Products

```text
product_id
product_name
category
price
```

### 🛒 Orders

```text
order_id
customer_id
product_id
quantity
order_date
```

Relationships:

```text
Customers
    │
    │ customer_id
    ▼
Orders
    │
    │ product_id
    ▼
Products
```

---

# 🧰 SQL Tool

The project implements a controlled SQL execution function:

```python
execute_read_only_sql(sql)
```

Its responsibilities include:

* Validate SQL
* Reject dangerous operations
* Execute valid queries
* Limit returned rows
* Return columns and rows
* Return errors safely

---

# 🛡️ SQL Security

Allowing an LLM to directly execute arbitrary SQL can be dangerous.

For example, these statements should not be executed by this learning agent:

```sql
DELETE FROM customers;
```

```sql
DROP TABLE orders;
```

```sql
UPDATE products SET price = 0;
```

```sql
PRAGMA table_info(customers);
```

The project therefore restricts execution to read-only `SELECT` queries.

---

# 🔐 Read-Only SQL Validation

The notebook validates SQL before execution.

It checks that:

```text
✅ Query starts with SELECT
```

and rejects statements containing operations such as:

```text
❌ INSERT
❌ UPDATE
❌ DELETE
❌ DROP
❌ ALTER
❌ CREATE
❌ REPLACE
❌ TRUNCATE
❌ ATTACH
❌ DETACH
❌ PRAGMA
```

It also rejects multiple SQL statements.

---

# ⚠️ Important Production Security Note

The SQL validation in this notebook is intentionally designed for learning.

For a production system, **do not rely only on keyword filtering**.

A production implementation should additionally use:

* 🔐 A dedicated read-only database user
* 🧱 SQL parser / AST validation
* ⏱️ Query timeouts
* 📊 Result-size limits
* 🔒 Database permissions
* 📝 Query auditing
* 🚨 Monitoring
* 🛡️ Prompt-injection defenses

The strongest database-level protection is to ensure that the credentials available to the AI agent **cannot modify data**.

---

# 🔄 Agent Workflow

The SQL Database AI Agent follows this process:

### 1️⃣ Receive Question

```text
"Which customer placed the most orders?"
```

### 2️⃣ Inspect Schema

The agent provides the database schema to the LLM.

### 3️⃣ Generate SQL

The LLM generates an appropriate `SELECT` statement.

### 4️⃣ Validate SQL

The SQL validator checks the generated query.

### 5️⃣ Execute SQL

The safe query is executed against SQLite.

### 6️⃣ Receive Result

The database returns rows.

### 7️⃣ Explain Result

The LLM converts the result into a natural-language answer.

### 8️⃣ Return Answer

The user receives a readable response.

---

# 🧪 Example Questions

The notebook tests questions such as:

```text
What products are in the Accessories category?
```

```text
Which customer placed the most orders?
```

```text
What is the total quantity of Laptop Pro units ordered?
```

```text
Show the top 3 customers by total order value.
```

```text
Which customers are from Chennai?
```

---

# 📓 Notebook Structure

The notebook is organized into practical sections:

```text
01. 🤖 Project Introduction
02. 🎯 Learning Objectives
03. 🏗️ Architecture
04. 📦 Install Dependencies
05. 🔐 OpenAI API Configuration
06. 🗄️ Create SQLite Database
07. 🔎 Inspect Database Schema
08. 🛡️ Build Safe SQL Tool
09. 🧠 Generate SQL with LLM
10. 🔄 Build SQL AI Agent
11. 🧪 Test Natural-Language Questions
12. 🔐 Test SQL Safety
13. 🎓 Interview Concepts
14. 🚀 Future Enhancements
15. 🎉 Conclusion
```

---

# 🛠️ Technologies

| Technology          | Purpose                               |
| ------------------- | ------------------------------------- |
| 🐍 Python           | Core programming language             |
| 🧠 OpenAI API       | LLM / GenAI capabilities              |
| 🗄️ SQLite          | Relational database                   |
| 📓 Jupyter Notebook | Development environment               |
| 📝 SQL              | Database querying                     |
| 🤖 AI Agent         | Natural-language database interaction |
| 🔐 SQL Validation   | Read-only query protection            |

---

# 📦 Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/sql-database-ai-agent.git
```

Move into the project:

```bash
cd sql-database-ai-agent
```

Install the OpenAI package:

```bash
pip install openai
```

Or:

```bash
pip install -r requirements.txt
```

---

# 🔐 API Key Configuration

The notebook uses:

```text
OPENAI_API_KEY
```

### Windows PowerShell

```powershell
$env:OPENAI_API_KEY="your-api-key"
```

### Linux / macOS

```bash
export OPENAI_API_KEY="your-api-key"
```

The notebook also uses `getpass()` if the environment variable is not already configured.

---

# ⚠️ API Key Security

Never hard-code your API key inside your notebook.

❌ Avoid:

```python
OPENAI_API_KEY = "sk-xxxxxxxxxxxxxxxx"
```

Instead use an environment variable:

```python
os.environ["OPENAI_API_KEY"]
```

Also add sensitive files to `.gitignore`.

---

# 📁 Recommended Repository Structure

```text
sql-database-ai-agent/
│
├── 📓 sql_database_ai_agent.ipynb
├── 📄 README.md
├── 📦 requirements.txt
├── 🚫 .gitignore
└── 🗄️ sales_ai_agent.db
```

The SQLite database can also be generated automatically by the notebook, so it does not have to be committed to GitHub.

---

# 📄 requirements.txt

Recommended contents:

```text
openai
```

SQLite is included with standard Python installations, so a separate SQLite package is normally unnecessary.

---

# 🚫 .gitignore

Recommended `.gitignore`:

```gitignore
# Python
__pycache__/
*.py[cod]

# Virtual environments
.venv/
venv/
env/

# Environment variables
.env

# Jupyter
.ipynb_checkpoints/

# IDE
.vscode/
.idea/

# Database
*.db
*.sqlite
*.sqlite3

# OS
.DS_Store
Thumbs.db
```

---

# ▶️ Running the Project

Start Jupyter Notebook:

```bash
jupyter notebook
```

Or:

```bash
jupyter lab
```

Open:

```text
sql_database_ai_agent.ipynb
```

Run the notebook cells from top to bottom.

---

# 🎓 Interview Questions

## ❓ What is Text-to-SQL?

Text-to-SQL is the process of converting natural-language questions into SQL queries.

Example:

```text
"Show customers from Chennai."
```

becomes:

```sql
SELECT *
FROM customers
WHERE city = 'Chennai';
```

---

## ❓ Why does the LLM need the database schema?

Without schema information, the LLM may generate SQL using tables or columns that do not exist.

Providing the schema helps ground SQL generation in the actual database structure.

---

## ❓ Is generating SQL enough to create an AI Agent?

Not necessarily.

A basic Text-to-SQL system can simply generate SQL.

An agentic system can additionally:

```text
Generate
   ↓
Execute
   ↓
Observe
   ↓
Correct / Continue
   ↓
Answer
```

This creates a more interactive tool-using workflow.

---

## ❓ Why validate LLM-generated SQL?

LLMs can generate unexpected or incorrect SQL.

Validation provides an additional safety layer before the query reaches the database.

---

## ❓ What is the best database permission for a production SQL Agent?

The AI agent should generally connect using a dedicated **read-only database account** when the use case only requires data retrieval.

---

## ❓ What happens if the generated SQL is incorrect?

A more advanced version of this project can send the database error back to the LLM and allow it to generate a corrected query.

For example:

```text
LLM
 ↓
SQL
 ↓
Database
 ↓
❌ SQL Error
 ↓
LLM
 ↓
Corrected SQL
 ↓
Database
 ↓
✅ Result
```

---

# 🚀 Future Enhancements

This project can be significantly expanded.

## 🔹 1. PostgreSQL / MySQL

Replace SQLite with:

```text
PostgreSQL
MySQL
Microsoft SQL Server
```

---

## 🔹 2. Automatic SQL Correction

Implement:

```text
Question
   ↓
Generate SQL
   ↓
Execute
   ↓
Error?
 ┌─┴─┐
Yes  No
 │    │
 ▼    ▼
LLM  Result
 │
 ▼
Correct SQL
```

---

## 🔹 3. Database RAG

For very large databases, retrieving only relevant schema information can improve performance.

```text
Database
   ↓
Schema Metadata
   ↓
Embeddings
   ↓
Vector Search
   ↓
Relevant Tables
   ↓
LLM
```

---

## 🔹 4. Data Visualization

The agent could automatically generate charts.

Example:

```text
"Show monthly sales."
```

↓

```text
SQL
 ↓
DataFrame
 ↓
Python
 ↓
📊 Chart
```

---

## 🔹 5. Streamlit UI

Add a conversational interface:

```text
┌───────────────────────────────────────┐
│       🤖 SQL Database AI Agent        │
├───────────────────────────────────────┤
│                                       │
│ 👤 Which customer ordered most?       │
│                                       │
│ 🤖 Karthik placed ...                 │
│                                       │
├───────────────────────────────────────┤
│ Ask your database a question...  [➤]  │
└───────────────────────────────────────┘
```

---

## 🔹 6. FastAPI Backend

A production architecture could become:

```text
React / Streamlit
       │
       ▼
    FastAPI
       │
       ▼
  AI SQL Agent
       │
       ▼
 PostgreSQL
```

---

## 🔹 7. MCP Integration

The SQL functionality can eventually be exposed through an MCP server:

```text
LLM
 │
 ▼
MCP Client
 │
 ▼
SQL MCP Server
 │
 ▼
Database
```

This connects naturally with your previous **MCP and AI Agent learning**.

---

# 🧠 Project Skills Demonstrated

By completing this project, you can demonstrate knowledge of:

```text
✅ Generative AI
✅ LLMs
✅ OpenAI API
✅ Text-to-SQL
✅ SQL
✅ SQLite
✅ Database Schema Understanding
✅ Function / Tool Execution
✅ AI Agents
✅ Prompt Engineering
✅ Structured LLM Output
✅ SQL Validation
✅ Database Security Concepts
✅ Agentic Workflows
```

---

# 💼 Resume Project Description

You can describe the project on your resume as:

> **SQL Database AI Agent** — Developed a GenAI-powered database assistant that converts natural-language questions into SQL using an LLM, validates queries through a read-only SQL execution layer, executes them against a relational database, and generates grounded natural-language explanations of query results.

---

# ⭐ GitHub Repository Information

### Repository Name

```text
sql-database-ai-agent
```

### Short Description

```text
🤖 GenAI SQL Database Agent that converts natural-language questions into SQL, safely executes read-only queries, and explains database results using an LLM.
```

### Suggested GitHub Topics

```text
ai-agent
ai-agents
generative-ai
genai
llm
openai
text-to-sql
sql
sqlite
database
python
agentic-ai
llm-agents
prompt-engineering
```

---

# 🌟 Key Takeaway

This project demonstrates an important transition from a traditional application:

```text
👤 User
   ↓
📝 SQL
   ↓
🗄️ Database
```

to a GenAI-powered application:

```text
👤 Natural Language
        ↓
     🧠 LLM
        ↓
    📝 SQL
        ↓
     🛡️ Safety
        ↓
    🗄️ Database
        ↓
    📊 Results
        ↓
     🧠 LLM
        ↓
    💬 Answer
```

This makes the project a strong foundation for progressing toward more advanced **Agentic AI, RAG, MCP, and enterprise GenAI systems**.

---

## 🚀 Next-Level Version

After completing this notebook, the project can be upgraded into a complete production-style application:

```text
                🤖 SQL AI Assistant
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
       🧠 LLM       🔎 Schema RAG   🛡️ Guardrails
          │             │             │
          └─────────────┼─────────────┘
                        ▼
                  🤖 SQL Agent
                        │
                  ┌─────┴─────┐
                  ▼           ▼
             📝 SQL       📊 Python
                  │           │
                  ▼           ▼
              🗄️ DB       📈 Charts
                  │           │
                  └─────┬─────┘
                        ▼
                  💬 AI Response
```

That version would turn this learning notebook into a much stronger **portfolio-level GenAI project**.
