# Hands-on lab 2

## Goal
The goal of the second hand-ons excercise is to familiarize ourselves with creating and managing roles, database objects and virtual warehouses.

### Step 1: Getting started
Open a new worksheet. 

### Step 2: Role creation
create the roles of DATA_ENGINEER and DATA_ANALYST with the code down below.
~~~~
create or replace role DATA_ENGINEER;
~~~~
### Step 3: Database-objects 
Next on we will create a example database, schema and table with the following code:
~~~~
create or replace database EXAMPLE_DATABASE;

create or replace schema EXAMPLE_DATABASE.EXAMPLE_SCHEMA;

create or replace table EXAMPLE_DATABASE.EXAMPLE_SCHEMA.EXAMPLE_TABLE ( 
    first_name string,
    last_name string
);
~~~~


### Step 4: Virtual Warehouses
Now lets create two virtual warehouses, one for the data-analyst and one for the data-engineer:
~~~~
create or replace warehouse ANALYST_WH
    auto_suspend = 120
    auto_resume = TRUE
    warehouse_size = small;
    
create or replace warehouse ENGINEER_WH
    auto_suspend = 140
    scaling_policy = ECONOMY
    auto_resume = FALSE
    min_cluster_count = 2
    max_cluster_count = 4
    warehouse_size = large;
~~~~
### Step 5: Granting access
Next on we will grant access to the virtual warehouses created for our data-engineer and data-analyst:
~~~~
grant all on warehouse COMPUTE_WH to role DATA_ANALYST;
grant all on warehouse ENGINEER_WH to role DATA_ANALYST;
grant all on database EXAMPLE_DATABASE to role DATA_ANALYST;
grant all on schema EXAMPLE_DATABASE.EXAMPLE_SCHEMA to role DATA_ANALYST;
~~~~
### Step 6: Using the warehouses
If we now select the data engineer role we can use the warehouse we just created, try it yourselves! 
~~~~
use role DATA_ENGINEER;

use warehouse ENGINEER_WH;
~~~~
