# Hands-on lab 2

## Goal
The goal of the second hand-on lab is to familiarize yourself with creating and managing roles, database objects and virtual warehouses.

### 1. Open a worksheet
Open a new worksheet:

<img src="https://github.com/foprel/snowflake-101-training/blob/main/images/worksheet-menu.png" width="325">
<img src="https://github.com/foprel/snowflake-101-training/blob/main/images/worksheet-add.png" width="200">

### 2. Create roles
create the roles of DATA_ENGINEER and DATA_ANALYST with the code down below. 
```sql
--Create roles
create or replace role DATA_ENGINEER;
create or replace role DATA_ANALYST;

-- Assign roles to accountadmin
grant role DATA_ENGINEER to role ACCOUNTADMIN;
grant role DATA_ANALYST to role ACCOUNTADMIN;

-- Assign roles to your user
grant role DATA_ENGINEER to user <USER_NAME>;
grant role DATA_ANALYST to user <USER_NAME>;
```

Try to add a description of the roles both for the DATA_ENGINEER and DATA_ANALYST. Hint: you can use Snowflake's [documentation](https://docs.snowflake.com/en/sql-reference/sql/create-role.html) to find the right commmand.

### 3. Create database objects 
Next, we will create an example database, schema and table with the following code:

```sql
create or replace database MARKETING;

create or replace schema MARKETING.WEBSITE;

create or replace table MARKETING.WEBSITE.EVENTS ( 
    id string,
    event_name string,
    event_type string,
    event_page string,
    event_timestamp timestamp
);
```

### 4. Create virtual warehouses
Now let's create two [virtual warehouses](https://docs.snowflake.com/en/sql-reference/sql/create-warehouse.html), one for the DATA_ENGINEER and one for the DATA_ANALYST role. With the parameters shown below you can edit the functionality as well as the size of the virtual warehouses: 

```sql
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
```

Notice how you can create warehouses with specifications for different requirements. 

###  5. Grant privileges
Next, we will [grant privileges](https://docs.snowflake.com/en/sql-reference/sql/grant-privilege.html) to the virtual warehouses created for our DATA_ENGINEER and DATA_ANALYST roles. With the following code you can edit who can acces which warehouse and database objects and to what [extent](https://docs.snowflake.com/en/user-guide/security-access-control-privileges.html). 

```sql
-- Grant access to warehouses
grant all on warehouse ANALYST_WH to role DATA_ANALYST;
grant all on warehouse ENGINEER_WH to role DATA_ENGINEER;

-- Grant access to databases
grant usage on database MARKETING to role DATA_ANALYST;
grant all on database MARKETING to role DATA_ENGINEER;

-- Grant access to schemas
grant usage on schema MARKETING.WEBSITE to role DATA_ANALYST;
grant all on schema MARKETING.WEBSITE to role DATA_ENGINEER;

-- Grant access to tables
grant select on table MARKETING.WEBSITE.EVENTS to role DATA_ANALYST;
grant ownership on table MARKETING.WEBSITE.EVENTS to role DATA_ENGINEER;
```

###  6. Use the roles and warehouses
If we now select the DATA_ENGINEER role we can use the warehouse we have just created. Let's try to alter a table using this role:

```sql
-- Select role
use role DATA_ENGINEER;

-- Select warehouse
use warehouse ENGINEER_WH; 

-- Alter table
alter table MARKETING.WEBSITE.EVENTS
    rename column event_page to event_url;
```

Try to perform the same alteration with the DATA_ANALYST role. Notice how you are not able to change the table because you have insufficient permissions.

###  7. Exploring data
For the final part of this excercise, let's forget about the database objects we have just created. We will draw some information from a Snowflake sample table.

An overview of the columns we will use can be created with the following code:

```sql
show columns in table SNOWFLAKE_SAMPLE_DATA.TPCDS_SF100TCL.ITEM;
```

Now let's find the average and median price and count per product category:

```sql
select
    I_CATEGORY,
    avg(I_CURRENT_PRICE),
    median(I_CURRENT_PRICE),
    count(I_CURRENT_PRICE)
from SNOWFLAKE_SAMPLE_DATA.TPCDS_SF100TCL.ITEM
group by I_CATEGORY;
```

That's it for Hands-On Lab #2. If you have some left, feel free to explore the data further with the charts functionality present in the Snowflake terminal. 
