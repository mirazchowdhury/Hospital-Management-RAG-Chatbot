# Hospital Management RAG Chatbot

A notebook based hospital management chatbot project that converts natural language hospital questions into safe PostgreSQL queries. The system reads live hospital database schema, stores schema documents in Milvus, retrieves the most relevant table descriptions for a user question, asks Gemini to generate SQL, validates the SQL, executes it through SQLAlchemy, and returns a concise answer.

## Project Summary

This repository contains a Retrieval Augmented Generation chatbot workflow for hospital management data. The project is built around two Jupyter notebooks.

1. `AI_code_module.ipynb`, which contains the main chatbot logic.
2. `hospital_management_chatbot_updated.ipynb`, which appears to be the Colab style setup, schema indexing, and testing notebook.

The project is designed for hospital administrators, doctors, nurses, and management users who want to ask database questions in natural language instead of writing SQL manually.

Example questions include:

```text
How many patients were admitted today?
Admissions by department
List currently admitted patients with department, ward, room, and bed
Which doctors are on shift right now?
Top prescribed medications
How many babies were born this month?
Which blood groups are available and how many units?
```

## Repository Link

```text
https://github.com/mirazchowdhury/Hospital-Management-RAG-Chatbot
```

## Repository Structure

```text
Hospital-Management-RAG-Chatbot/
    AI_code_module.ipynb
    hospital_management_chatbot_updated.ipynb
```

## File Details

## AI_code_module.ipynb

This notebook contains the main Python logic for the chatbot module. It can be converted into a reusable Python file such as:

```text
hospital_management_chatbot.py
```

Main responsibilities:

1. Connect to PostgreSQL using SQLAlchemy.
2. Connect to Milvus vector database.
3. Build table schema documents from the live PostgreSQL schema.
4. Create vector embeddings with Sentence Transformer.
5. Store schema embeddings in Milvus.
6. Retrieve relevant schema documents for a user question.
7. Detect hospital question intent.
8. Resolve time filters such as today, this month, last seven days, and last thirty days.
9. Generate PostgreSQL SQL with Gemini.
10. Sanitize and validate generated SQL before execution.
11. Execute safe read only SQL queries.
12. Return summarized query results.

## hospital_management_chatbot_updated.ipynb

This notebook works as the setup and testing notebook. It imports the generated chatbot module, reloads it, indexes the database schema into Milvus, and tests many hospital management questions.

The notebook shows test examples for:

1. Admission count.
2. Daily admissions.
3. Department wise admissions.
4. Currently admitted patients.
5. Discharges.
6. Critical patients.
7. Patient vitals.
8. Birth and newborn statistics.
9. Abortion case records.
10. Staff counts.
11. Staff shifts.
12. Prescriptions.
13. Nurse workload.
14. Blood bank questions.
15. Organ donation questions.
16. Surgery questions.

## Core System Workflow

```text
User question
    Intent detection
        Schema retrieval from Milvus
            Gemini SQL generation
                SQL sanitization
                    SQL validation
                        PostgreSQL execution
                            Result summarization
```

## Architecture

```text
PostgreSQL hospital database
    SQLAlchemy inspector reads schema
        Table docs are created
            Sentence Transformer creates vectors
                Milvus stores schema vectors

User asks a question
    Query vector is created
        Milvus retrieves relevant schema docs
            Gemini generates SQL
                Validator blocks unsafe SQL
                    SQLAlchemy executes query
                        Chatbot returns answer
```

## Main Technologies

| Area | Technology |
| :-- | :-- |
| Notebook environment | Jupyter Notebook or Google Colab |
| Database | PostgreSQL |
| Database access | SQLAlchemy |
| Vector database | Milvus |
| Embedding model | Sentence Transformers |
| Embedding checkpoint | `intfloat/e5-base-v2` |
| LLM provider | Google Gemini |
| SQL dialect | PostgreSQL |
| Time zone support | Asia Dhaka |
| Main task | Natural language to SQL over hospital data |

## Required Python Packages

The notebooks use the following packages:

```text
sqlalchemy
psycopg2-binary
pymilvus
sentence-transformers
google-generativeai
google-api-core
```

For notebook execution, also install:

```text
jupyter
ipykernel
python-dotenv
```

## Suggested requirements.txt

Create a file named `requirements.txt` in the repository root.

```text
sqlalchemy
psycopg2-binary
pymilvus
sentence-transformers
google-generativeai
google-api-core
jupyter
ipykernel
python-dotenv
```

## Environment Variables

The chatbot uses environment variables for database, Milvus, and Gemini configuration.

## PostgreSQL Variables

```env
REAL_HOST=your_postgres_host
REAL_PORT=5432
PG_USER=postgres
PG_PASSWORD=your_postgres_password
PG_DB=hospital
```

## Milvus Variables

```env
MILVUS_URI=your_milvus_uri
MILVUS_TOKEN=your_milvus_token
MILVUS_COLLECTION=db_schema_docs_v2
```

## Gemini Variables

```env
GEMINI_API_KEY=your_gemini_api_key
GEMINI_SQL_MODEL=models/gemini-2.0-flash-lite-001
```

## Optional Variables

```env
SCHEMA_VERSION=v1
EMBED_DIM=768
```

## Installation

Clone the repository.

```bash
git clone https://github.com/mirazchowdhury/Hospital-Management-RAG-Chatbot.git
cd Hospital-Management-RAG-Chatbot
```

Create a virtual environment.

```bash
python -m venv .venv
```

Activate the environment on Windows.

```bash
.venv\Scripts\activate
```

Activate the environment on Linux or macOS.

```bash
source .venv/bin/activate
```

Install dependencies.

```bash
pip install sqlalchemy psycopg2-binary pymilvus sentence-transformers google-generativeai google-api-core jupyter ipykernel python-dotenv
```

Start Jupyter Notebook.

```bash
jupyter notebook
```

Then open:

```text
hospital_management_chatbot_updated.ipynb
```

## Recommended Setup Flow

Follow this order for a clean run.

1. Prepare a PostgreSQL hospital database.
2. Prepare a Milvus instance.
3. Create a Gemini API key.
4. Set all required environment variables.
5. Open the notebook.
6. Convert the module notebook into a Python module if needed.
7. Import the module as `hospital_management_chatbot`.
8. Run schema indexing.
9. Run sample chatbot questions.
10. Review SQL output and answers.

## Converting Notebook Code Into a Python Module

The setup notebook imports:

```python
import hospital_management_chatbot
```

For that import to work, export the main logic from `AI_code_module.ipynb` into:

```text
hospital_management_chatbot.py
```

One simple way is to create a Python file and paste the code cells from `AI_code_module.ipynb`.

Recommended file layout:

```text
Hospital-Management-RAG-Chatbot/
    hospital_management_chatbot.py
    hospital_management_chatbot_updated.ipynb
    AI_code_module.ipynb
    requirements.txt
    README.md
```

## Milvus Schema Indexing

The notebook indexes database schema into Milvus by calling:

```python
hospital_management_chatbot.init_once(index=True)
```

This function:

1. Connects to PostgreSQL.
2. Reads all table names.
3. Reads columns, primary keys, and foreign keys.
4. Adds known join paths for hospital tables.
5. Converts each table description into a vector.
6. Stores the vector and table metadata in Milvus.

## Milvus Collection Design

The schema collection contains fields similar to:

```text
id
embedding
doc_type
table_name
contains_phi
hospital_scoped
schema_version
text
```

The vector index uses HNSW with cosine similarity.

## Main Chat Function

After indexing, questions are answered through:

```python
chat(question: str, hospital_id: int) -> str
```

Example:

```python
from hospital_management_chatbot import chat

answer = chat(
    question="How many patients were admitted today?",
    hospital_id=1
)

print(answer)
```

## Intent Detection

The chatbot has intent logic for common hospital questions.

Supported intent groups include:

| Intent Group | Related Tables |
| :-- | :-- |
| Admissions by department | admissions, departments |
| Daily admission series | admissions |
| Admissions today | admissions |
| Current admitted patient list | admissions, patients, departments, wards, rooms, beds |
| Discharges | discharges, admissions, patients |
| Critical patients | patient conditions, admissions, patients, departments |
| Vitals | patient vitals, admissions, patients |
| Birth records | births, newborns, patients |
| Abortion cases | abortion cases, patients, staff |
| Staff counts | staff, staff roles, staff department, departments |
| Shift queries | staff shifts, staff, staff roles, departments |
| Prescriptions | prescriptions, prescription items, medications, admissions, patients, staff |
| Nurse assignments | nurse assignments, admissions, patients, staff, wards, rooms, beds |
| Blood bank | blood bank inventory, blood groups, donors, donations, patients |
| Organ donation | donations, organ donation items, organs, donors |
| Surgery | surgeries, surgery team, admissions, patients, staff |

## Time Window Handling

The chatbot can resolve common time expressions.

Supported examples:

```text
today
this month
last 7 days
last 30 days
last N days
YYYY MM DD HH MM SS
```

The system uses the latest admission timestamp from the database when available. Otherwise, it falls back to the Asia Dhaka time zone.

## SQL Generation Rules

Gemini is prompted to generate PostgreSQL SQL with strict rules.

Key rules:

1. Output only one SQL query.
2. Use only SELECT or WITH.
3. Use only tables and columns found in retrieved schema context.
4. Apply hospital filter only on tables that contain `hospital_id`.
5. Use `:hospital_id` placeholder instead of hardcoded hospital id.
6. Use time parameters such as `:start_ts`, `:end_ts`, and `:at_ts` when available.
7. Prefer join paths from the schema document.
8. Avoid code fences and semicolons.
9. Limit list queries to 200 rows.
10. For current patient condition, use latest record per admission.

## SQL Safety Features

The project includes multiple safety checks before query execution.

## Query Sanitization

The sanitizer removes code fences and trailing semicolons from generated SQL.

## Allowed Query Types

Only these query starts are allowed:

```text
SELECT
WITH
```

## Forbidden Keywords

The validator rejects SQL containing write or schema mutation operations.

```text
insert
update
delete
drop
alter
truncate
create
grant
revoke
vacuum
copy
```

## Hospital Filter Check

The SQL must include:

```text
:hospital_id
```

This helps keep answers scoped to one hospital.

## Table Validation

The SQL may only use tables from the retrieved schema context.

## Column Validation

The system uses SQLAlchemy inspector to verify that referenced columns exist in the selected tables.

## Safe Execution

The query is wrapped in an outer limit.

```sql
WITH q AS (
    generated_query_here
)
SELECT * FROM q LIMIT :_limit
```

Default maximum row count:

```text
200
```

## Result Summarization

If the query returns zero rows, the chatbot returns:

```text
No matching records found.
```

If the query returns ten rows or fewer, all rows are shown.

If the query returns more than ten rows, the chatbot summarizes the row count and displays the first ten rows.

## Example Questions

```text
How many patients were admitted today?
Daily admissions count for the last 7 days
Admissions by department
Which department has the highest admissions?
Most frequent admission reasons
List currently admitted patients with department, ward, room, bed
How many patients have been discharged?
Show the most recent discharged patients
Which patients are currently critical?
Latest vitals for critical patients
How many babies were born today?
How many babies were born this month?
How many newborns died after birth?
How many abortion cases were performed this month?
List recent abortion cases with procedure type and date
Total staff count by role
Staff distribution by department and role
Which doctors are on shift right now?
Top prescribed medications
Nurse workload, number of active patients per nurse
Which blood groups are available and how many units?
Organ donations by organ type
List upcoming surgeries
```

## Expected Database Tables

The notebook contains join support for hospital tables such as:

```text
patients
admissions
discharges
diagnoses
patient_conditions
patient_vitals
prescriptions
prescription_items
medications
nurse_assignments
births
newborns
abortion_cases
donations
donors
organ_donation_items
organs
surgeries
surgery_team
blood_bank_inventory
blood_groups
staff
staff_roles
staff_department
staff_shifts
departments
wards
rooms
beds
```

The real database schema can contain more tables. The indexing function reads the live schema from PostgreSQL.

## Security and Privacy Notes

This project works with hospital data, which may include patient health information. Use strict privacy controls before running it on real records.

Recommended practices:

1. Do not commit database credentials.
2. Do not commit Gemini API keys.
3. Do not commit Milvus tokens.
4. Use a read only PostgreSQL database user.
5. Restrict database access to required tables only.
6. Log only safe metadata.
7. Avoid logging patient names, phone numbers, addresses, diagnosis notes, and prescriptions.
8. Add role based access before using this with staff users.
9. Keep audit logs for every question and generated SQL.
10. Add human review for sensitive operational decisions.

## Important Current State Notes

1. The repository currently contains notebooks only.
2. No `README.md` file was visible in the repository.
3. No `requirements.txt` file was visible in the repository.
4. No license file was visible in the repository.
5. The main logic should be exported into a `.py` module before normal Python import.
6. The setup notebook output shows that some test questions passed and some failed during validation or provider rate limits.
7. The project depends on live external services, including PostgreSQL, Milvus, and Gemini.
8. Query accuracy depends on database schema quality and the generated SQL.

## Common Errors and Fixes

## Missing Postgres environment variables

Error cause:

`REAL_HOST`, `REAL_PORT`, or `PG_PASSWORD` is missing.

Fix:

Set all PostgreSQL variables before running the notebook.

```bash
export REAL_HOST=your_host
export REAL_PORT=5432
export PG_USER=postgres
export PG_PASSWORD=your_password
export PG_DB=hospital
```

On Windows PowerShell:

```powershell
$env:REAL_HOST="your_host"
$env:REAL_PORT="5432"
$env:PG_USER="postgres"
$env:PG_PASSWORD="your_password"
$env:PG_DB="hospital"
```

## Missing Milvus environment variables

Error cause:

`MILVUS_URI` or `MILVUS_TOKEN` is missing.

Fix:

Set Milvus credentials.

```bash
export MILVUS_URI=your_milvus_uri
export MILVUS_TOKEN=your_milvus_token
```

## Missing Gemini API key

Error cause:

`GEMINI_API_KEY` is not set.

Fix:

Set the key.

```bash
export GEMINI_API_KEY=your_key
```

## Module not found

Error cause:

The notebook tries to import `hospital_management_chatbot`, but the Python file does not exist.

Fix:

Export code from `AI_code_module.ipynb` into:

```text
hospital_management_chatbot.py
```

## SQL validation failed

Possible causes:

1. Gemini generated a table not retrieved from schema context.
2. Gemini generated a wrong column name.
3. The SQL missed `:hospital_id`.
4. The selected table does not contain the expected hospital filter.
5. The question needs extra schema tables.

Fix options:

1. Reindex schema.
2. Increase retrieval count.
3. Add must have tables for the intent.
4. Improve the prompt.
5. Add extra join documentation.

## Gemini rate limit

Possible cause:

Too many requests were sent in a short time.

Fix options:

1. Use a model with higher quota.
2. Add longer retry delay.
3. Cache generated SQL.
4. Reduce test batch size.

## Recommended Improvements

1. Convert notebook logic into a clean Python package.
2. Add a real `requirements.txt`.
3. Add `.env.example`.
4. Add a sample configuration guide.
5. Add FastAPI endpoints for chatbot queries.
6. Add a simple web dashboard for hospital users.
7. Add structured JSON response format.
8. Add generated SQL preview for admin users.
9. Add audit logging.
10. Add role based access.
11. Add automated tests for SQL validation.
12. Add sample database schema for demo use.
13. Add Docker Compose for PostgreSQL and Milvus.
14. Add provider fallback when Gemini fails.
15. Add schema reindex command.
16. Add SQL execution timeout.
17. Add per user query rate limits.
18. Add patient data masking.
19. Add a license file.
20. Add a project diagram.

## Suggested Future Project Structure

```text
Hospital-Management-RAG-Chatbot/
    app/
        api/
            routes.py
        core/
            config.py
        database/
            postgres.py
            milvus.py
        services/
            schema_indexer.py
            schema_retriever.py
            sql_generator.py
            sql_validator.py
            chatbot_service.py
        schemas/
            chat_schema.py

    notebooks/
        AI_code_module.ipynb
        hospital_management_chatbot_updated.ipynb

    tests/
        test_sql_validator.py
        test_intent_detection.py

    hospital_management_chatbot.py
    requirements.txt
    .env.example
    README.md
```

## Suggested API Design

A future API can expose this endpoint:

```text
POST /chat
```

Request body:

```json
{
    "question": "How many patients were admitted today?",
    "hospital_id": 1
}
```

Response body:

```json
{
    "answer": "admission_count: 24",
    "sql_used": "SELECT COUNT(*) AS admission_count FROM admissions WHERE hospital_id = :hospital_id",
    "row_count": 1
}
```

For production, return `sql_used` only to admins.

## Suggested .gitignore

```text
.venv/
__pycache__/
*.pyc
.env
.ipynb_checkpoints/
.idea/
*.log
```

## License

No license file was visible in the repository when this README was prepared. Add a license before public reuse or deployment.

## Author

Miraz Uddin Chowdhury

Repository:

```text
https://github.com/mirazchowdhury/Hospital-Management-RAG-Chatbot
```
