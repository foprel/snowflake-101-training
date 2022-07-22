# Hands-on lab 2

## Goal
The goal of the second hand-ons excercise is to familiarize ourselves with creating and managing roles, database objects and virtual warehouses.

### 1. Getting started
Open a new worksheet, as shown during the previous hands-one. 

### 2. Role creation
create the roles of DATA_ENGINEER and DATA_ANALYST with the code down below. Bonus points: try to add a description of the roles both for the data-engineer and data-analyst. 
~~~~
create or replace role DATA_ENGINEER;
~~~~
### 3. Database-objects 
Next on we will create a example database, schema and table with the following code:
~~~~
create or replace database EXAMPLE_DATABASE;

create or replace schema EXAMPLE_DATABASE.EXAMPLE_SCHEMA;

create or replace table EXAMPLE_DATABASE.EXAMPLE_SCHEMA.EXAMPLE_TABLE ( 
    first_name string,
    last_name string
);
~~~~


### 4. Virtual Warehouses
Now lets create two virtual warehouses, one for the data-analyst and one for the data-engineer. With the parameters shown below you can edit the functionality as well as the size of the virtual warehouses. 
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
###  5. Access management
Next on we will grant access to the virtual warehouses created for our data-engineer and data-analyst, with the following code you can edit who can acces which warehouses and database objects and to what extend. 
~~~~
grant all on warehouse COMPUTE_WH to role DATA_ANALYST;
grant all on warehouse ENGINEER_WH to role DATA_ANALYST;
grant all on database EXAMPLE_DATABASE to role DATA_ANALYST;
grant all on schema EXAMPLE_DATABASE.EXAMPLE_SCHEMA to role DATA_ANALYST;
~~~~
###  6. Using the warehouses
If we now select the data engineer role we can use the warehouse we just created, try it yourselves! 
~~~~
use role DATA_ENGINEER;

use warehouse ENGINEER_WH;
~~~~
###  7. Data exploration 
Let's now draw some basic information from the sample datasets already present in Snowflake. 

An overview of the columns we will use can be created with the following code:
~~~~
show columns in table SNOWFLAKE_SAMPLE_DATA.TPCDS_SF100TCL.ITEM;
~~~~

Now let's find the average and median price and count per product category:
~~~~
select avg(I_CURRENT_PRICE), median(I_CURRENT_PRICE), count(I_CURRENT_PRICE), I_CATEGORY
from SNOWFLAKE_SAMPLE_DATA.TPCDS_SF100TCL.ITEM;
~~~~

That's it for this hands-on! But if you have some left, feel free to explore the data further with the charts functionality present in the terminal. 
