📊 Netflix Data Analysis Project (dbt)

This repository contains a fully functional dbt (Data Build Tool) project designed to transform raw Netflix datasets into analytics-ready models.
The project cleans, standardizes, and models Netflix titles and viewing history to deliver insights on content performance and user engagement.

🚀 Project Overview

The goal of this project is to build a production-grade transformation pipeline using dbt.
It takes raw Netflix metadata and viewing logs and produces:

Clean staging models

Curated dimension and fact tables

Analytical models for content performance and retention insights

Tests, documentation, lineage, and snapshots

Seeds and macros for additional data enrichment

The project follows dbt best practices: modular SQL, documentation, version control, testing, and lineage.

🎯 Project Goals

Clean raw Netflix data into structured staging models

Build dimensions (titles, users) and fact tables (viewing sessions)

Create analytics models such as top-watched titles and retention trends

Apply dbt tests to ensure data quality

Generate lineage graphs and documentation

Enable reproducible local development and future extensibility

🛠️ Tech Stack

dbt-core

dbt-postgres (or adapter of your choice)

SQL + Jinja templating

CSV seeds

Snapshots

Optional visualization: Power BI, Tableau, Metabase

📁 Repository Structure

.
├── dbt_project.yml
├── README.md
├── packages.yml
├── models/
│   ├── staging/
│   │   ├── stg_netflix_titles.sql
│   │   ├── stg_viewing_history.sql
│   │   └── stg_users.sql
│   ├── marts/
│   │   ├── core/
│   │   │   ├── dim_titles.sql
│   │   │   └── fct_viewing_sessions.sql
│   │   └── analytics/
│   │       ├── top_titles_by_views.sql
│   │       └── retention_analysis.sql
│   └── marts.yml
├── seeds/
│   └── lookup_genres.csv
├── snapshots/
│   └── titles_snapshot.sql
├── macros/
│   └── utils.sql
└── analyses/
    └── exploratory_queries.sql



📦 Installation & Setup
1. Clone the repository
git clone https://github.com/UsmanMatheen/Netflix_Data_Analysis_Project_dbt-databuildtool.git
cd Netflix_Data_Analysis_Project_dbt-databuildtool

2. (Optional) Create a virtual environment
python -m venv .venv
source .venv/bin/activate  # Mac/Linux
.venv\Scripts\activate     # Windows

3. Install dbt + adapter (example: Postgres)
pip install dbt-core dbt-postgres

4. Install dbt packages
dbt deps

🔧 Configuring dbt (profiles.yml)

Your profiles.yml should be stored in:

~/.dbt/profiles.yml

Example (Postgres)
netflix_dbt:
  target: dev
  outputs:
    dev:
      type: postgres
      host: YOUR_HOST
      user: YOUR_USER
      password: YOUR_PASSWORD
      port: 5432
      dbname: YOUR_DB
      schema: dbt_netflix
      threads: 4

📘 Working With Seeds

This project uses seed files such as lookup_genres.csv.

Load seeds:

dbt seed

🧱 Models
1. Staging Models (/staging)

Light transformations:

casting types

standardizing naming

trimming raw fields

Example: stg_netflix_titles.sql

with raw as (
    select * from {{ source('raw', 'netflix_titles') }}
)

select
    id as title_id,
    title,
    lower(trim(type)) as content_type,
    release_year::int as release_year,
    listed_in as genres_raw,
    split_part(listed_in, ',', 1) as primary_genre,
    duration,
    date_trunc('day', date_added) as date_added
from raw

2. Core Models (/marts/core)

✔ dim_titles.sql – dimension table for Netflix titles
✔ fct_viewing_sessions.sql – fact table for viewing activity

3. Analytics Models (/marts/analytics)

✔ Trending content
✔ Retention analysis
✔ Top viewed titles

🧪 Tests

dbt tests are defined in marts.yml and include:

unique

not_null

relationships

accepted_values

Example:

models:
  - name: dim_titles
    columns:
      - name: title_id
        tests:
          - unique
          - not_null

🕒 Snapshots

The project includes a snapshot for tracking changes in title metadata:

/snapshots/titles_snapshot.sql

{% snapshot titles_snapshot %}
{{ config(
    target_schema='snapshots',
    unique_key='title_id',
    strategy='timestamp',
    updated_at='last_updated_at'
) }}

select * from {{ ref('stg_netflix_titles') }}

{% endsnapshot %}

📈 Running dbt

Run the full pipeline:

dbt run


Run tests:

dbt test


Build everything (run + test + docs):

dbt build


Generate and view documentation:

dbt docs generate
dbt docs serve

📊 Example Analyses

Located in /analyses/exploratory_queries.sql:

Includes sample analytical queries such as:

Top 10 titles by watch time

Viewing frequency by user

Monthly retention and engagement patterns

Genre distribution analysis

📉 Visualization & Dashboarding

Once marts are materialized, connect directly to:

Power BI

Tableau

Metabase

Looker

Superset

Suggested dashboards:

Content performance

Genre trends

User engagement

Retention and churn signals

🤖 CI/CD (Optional GitHub Workflow Included)

A ready-to-use GitHub Actions workflow is included to:

Install dbt

Configure profiles

Run dbt seed, dbt deps, dbt build

Validate transformations on pull requests

Located at:

.github/workflows/dbt.yml

📜 License

This project is open-source under the MIT License.

👤 Author

Usman Matheen

GitHub: https://github.com/UsmanMatheen

Email: contact.matheen9@gmail.com
