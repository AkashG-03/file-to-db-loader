# File-to-DB Loader

A Python-based ETL pipeline that reads retail CSV files, processes them in chunks, and loads the data into PostgreSQL using Pandas and SQLAlchemy.

## Overview

This project demonstrates a practical file-to-database data engineering workflow.

The application:

1. Reads retail CSV files from the filesystem.
2. Reads column definitions from `schemas.json`.
3. Processes large CSV files in chunks of 10,000 rows.
4. Converts each chunk into a Pandas DataFrame.
5. Loads the data into PostgreSQL using SQLAlchemy.
6. Uses environment variables for database configuration.

## Architecture

```text
Retail CSV Files
       |
       v
  schemas.json
       |
       v
   Pandas
 read_csv()
       |
       | chunksize=10000
       v
 DataFrame Chunks
       |
       v
  SQLAlchemy
       |
       v
  PostgreSQL
```

## Features

- CSV-to-PostgreSQL data loading
- Schema-driven column mapping
- Chunk-based processing for large files
- Pandas DataFrame processing
- SQLAlchemy database connectivity
- PostgreSQL integration
- Environment-based configuration
- Command-line dataset selection
- Handles empty CSV fields using `keep_default_na=False`

## Datasets

The project processes the following retail datasets:

- `departments`
- `categories`
- `orders`
- `products`
- `customers`
- `order_items`

## Technologies

- Python
- Pandas
- PostgreSQL
- SQLAlchemy
- psycopg2
- python-dotenv

## Project Structure

```text
file-to-db-loader/
│
├── app.py
├── requirements.txt
├── .gitignore
│
└── data/
    └── retail_db/
        └── schemas.json
```

The `.env` file and Python virtual environment are intentionally excluded from Git.

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/AkashG-03/file-to-db-loader.git
cd file-to-db-loader
```

### 2. Create a virtual environment

Windows PowerShell:

```powershell
python -m venv projectftdvenv
```

Activate it:

```powershell
projectftdvenv\Scripts\activate
```

Git Bash:

```bash
source projectftdvenv/Scripts/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

## Configuration

Create a `.env` file in the project root:

```env
SRC_BASE_DIR=data/retail_db
DB_HOST=localhost
DB_PORT=5432
DB_NAME=itversity_retail_db
DB_USER=itversity_retail_user
DB_PASS=your_password
```

Update these values according to your PostgreSQL configuration.

> Do not commit `.env` to GitHub because it can contain database credentials.

## Running the Loader

### Load all datasets

```bash
python app.py
```

### Load a specific dataset

Git Bash:

```bash
python app.py '["products"]'
```

PowerShell:

```powershell
python app.py "[`"products`"]"
```

### Load multiple datasets

```bash
python app.py '["orders", "order_items"]'
```

## Chunk Processing

The loader uses Pandas chunk processing:

```python
pd.read_csv(
    file,
    names=columns,
    chunksize=10000,
    keep_default_na=False
)
```

Instead of loading an entire CSV file into memory, the file is processed in smaller chunks.

For example:

```text
Large CSV
   |
   +---- Chunk 0 ----> PostgreSQL
   |
   +---- Chunk 1 ----> PostgreSQL
   |
   +---- Chunk 2 ----> PostgreSQL
   |
   +---- ...
```

This allows the application to work with larger datasets while reducing memory usage.

## Schema-Driven Processing

The application uses `schemas.json` to determine the column names for each dataset.

The process is:

```text
schemas.json
     |
     v
Identify dataset
     |
     v
Sort columns by column_position
     |
     v
Create DataFrame
     |
     v
Load into PostgreSQL
```

## Database Loading

Each DataFrame chunk is inserted using:

```python
df.to_sql(
    ds_name,
    db_conn_uri,
    if_exists='append',
    index=False
)
```

`if_exists='append'` is important because the loader processes one dataset across multiple chunks. Each chunk is appended to the target table.

## Important Note About Re-running

This project is intended for loading the source data into the target tables.

If the target tables already contain the same records, running the loader again can result in duplicate primary-key errors.

For a fresh practice reload, clear the target tables first:

```sql
TRUNCATE TABLE
    order_items,
    orders,
    products,
    customers,
    categories,
    departments
CASCADE;
```

Only execute this when it is safe to remove the existing data.

## Environment Variables

The application reads database configuration using `python-dotenv`:

```python
load_dotenv()
```

and:

```python
os.environ.get('DB_HOST')
os.environ.get('DB_PORT')
os.environ.get('DB_NAME')
os.environ.get('DB_USER')
os.environ.get('DB_PASS')
```

This keeps database configuration separate from the application code.

## Learning Outcomes

This project demonstrates practical data engineering concepts including:

- ETL pipeline development
- File-based data ingestion
- CSV processing
- Pandas DataFrames
- Chunk-based processing
- Schema-driven data loading
- PostgreSQL
- SQLAlchemy
- Environment variables
- Command-line arguments
- Handling large datasets

## Author

**Akash G**

GitHub: https://github.com/AkashG-03
