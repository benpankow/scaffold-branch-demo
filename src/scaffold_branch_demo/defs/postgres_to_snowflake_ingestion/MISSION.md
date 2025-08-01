# Mission: Postgres to Snowflake Product Data Ingestion

## Task Overview
This branch addresses GitHub issue #1: "REQUEST: Ingest product data from Postgres database into Snowflake" using the dagster-sling integration.

## What Has Been Completed

### 1. Package Installation
- Installed `dagster-embedded-elt` package which includes `dagster-sling`
- Dependencies synchronized with `uv sync`

### 2. Component Scaffolding
- Scaffolded `dagster_sling.SlingReplicationCollectionComponent` at `src/scaffold_branch_demo/defs/postgres_to_snowflake_ingestion/`
- Created basic structure with `defs.yaml` and `replication.yaml` files

### 3. Configuration Setup
- Configured `defs.yaml` with:
  - Postgres source connection (source_postgres) 
  - Snowflake target connection (target_snowflake)
  - Environment variable placeholders for all connection parameters
- YAML validation passes successfully

## Current State
- **defs.yaml**: Fully configured with connection details using environment variables
- **replication.yaml**: Contains basic template structure but needs manual population

## Next Steps Required

### 1. Complete replication.yaml Configuration
The `replication.yaml` file needs to be manually updated with the actual Sling replication configuration:

```yaml
source: source_postgres
target: target_snowflake

streams:
  products:
    select: "SELECT * FROM products"
    object: "analytics.products"
    primary_key: "id"
    mode: full-refresh
    
  product_categories:
    select: "SELECT * FROM product_categories"
    object: "analytics.product_categories"
    primary_key: "id"
    mode: full-refresh
    
  product_reviews:
    select: "SELECT * FROM product_reviews"
    object: "analytics.product_reviews"
    primary_key: "id"
    mode: incremental
    update_key: "updated_at"
```

### 2. Environment Variable Setup
Configure the following environment variables:

**Postgres Source:**
- `POSTGRES_HOST`
- `POSTGRES_PORT`
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `POSTGRES_DATABASE`

**Snowflake Target:**
- `SNOWFLAKE_HOST`
- `SNOWFLAKE_USER`
- `SNOWFLAKE_PASSWORD`
- `SNOWFLAKE_DATABASE`
- `SNOWFLAKE_SCHEMA`
- `SNOWFLAKE_WAREHOUSE`
- `SNOWFLAKE_ROLE`

### 3. Database Schema Validation
- Verify that the source Postgres database contains the expected tables:
  - `products` (with `id` primary key)
  - `product_categories` (with `id` primary key)
  - `product_reviews` (with `id` primary key and `updated_at` timestamp)

### 4. Target Schema Creation
- Ensure the Snowflake target database has the `analytics` schema created
- Verify permissions allow creating tables in the target schema

### 5. Testing and Validation
- Run `dg check defs` to validate the complete configuration
- Test the connection to both source and target databases
- Perform a test run of the Sling replication
- Validate data transfer and transformation

### 6. Asset Metadata Enhancement
Consider adding asset metadata for better observability:
- Asset descriptions explaining each table's purpose
- Tags for grouping and filtering (source="postgres", destination="snowflake")
- Owners assignment to the analytics team
- Kind tags for Dagster UI visualization

## Technical Notes
- Using Sling's full-refresh mode for reference tables (products, product_categories)
- Using incremental mode for transaction tables (product_reviews) based on `updated_at` timestamp
- All connections configured with environment variables for security
- Component follows Dagster best practices for ELT pipelines

## Integration Details
- **Source**: Postgres database with product-related tables
- **Target**: Snowflake analytics schema
- **Tool**: Sling for efficient data replication
- **Framework**: Dagster for orchestration and monitoring
- **Purpose**: Enable product analytics on ingested data