# Hands-on lab 3

## Goal
The goal of this third hands-on lab is to familiarize you with some of Snowflake's unique features. In this lab you will learn how to get open-source data from the marketplace, to use zero-copy cloning to replicate data, to apply data masking to shield sensitive information and to handle semi-structured data.

## Excercises



### 2.

```sql
use role XYZ;

create or replace table XYZ
    clone ABC;
```

### 3. Create and apply masking policies
Snowflake allows you to easily mask data for specific user roles. Use the following code to mask data for all roles except the DATAANALYST and DATAENGINEER roles.


**3.1 Create a policy to mask a string field:**
```sql
create or replace masking policy simple_mask_string as (val string) returns string ->
    case
        when current_role() in ('DATAANALYST', 'DATAENGINEER') then val
        else '*** masked ***'
    end;
```

**3.2 Create a policy to mask an integer field:**
```sql
create or replace masking policy simple_mask_int as (val integer) returns integer ->
    case
        when current_role() in ('DATAANALYST', 'DATAENGINEER') then val
        else -999
    end;
```

**3.3 Apply the masking policies to certain columns in the table:**

```sql
alter table XYZ modify
    COLUMN_ABC set masking policy simple_mask_string,
    COLUMN_DEF set masking policy simple_mask_int,
```

### 4. Handle semi-structured data
Snowflake makes it easy to handle semi-structured data files, such as CSV, JSON, Avro and Parquet. Use the following code to load your first semi-structured data into snowflake:

**4.1 Load csv file into Snowflake**
```sql
create or replace temporary table example_data (
    abc string,
    def string,
    ghi string
);
```

**4.2 Create a new file format**
```sql
create or replace file format tutorial_csv
    field_delimiter = ','
    record_delimiter='\n';
```

**4.3 Create temporary stage**
```sql
create or replace temporary stage tutorial_stage
    file_format = tutorial_csv;
```

**4.4 Stage the csv file**
```sql
PUT file://%TEMP%/tutorial_file.csv @tutorial_stage;
```

**4.5 Load the csv dat into a relational table**
copy into tutorial_table(
    abc,
    def,
    ghi
)
from (
    select substr(parse_json($1))
)