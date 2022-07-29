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
grant role DATA_ENGINEER to role SYSADMIN;
grant role DATA_ANALYST to role SYSADMIN;

-- Assign roles to your user
grant role DATA_ENGINEER to user <USER_NAME>;
grant role DATA_ANALYST to user <USER_NAME>;
```

Try to add a description of the roles both for the DATA_ENGINEER and DATA_ANALYST. Hint: you can use Snowflake's [documentation](https://docs.snowflake.com/en/sql-reference/sql/create-role.html) to find the right commmand.

Go to `Admin > Users & Roles > Roles` to review your new role hierarchy. Notice how DATA_ANALYST and DATA_ENGINEER roles are nested under SYSADMIN:

<img src="https://github.com/foprel/snowflake-101-training/blob/main/images/user-roles.PNG" width="400">

### 3. Create database objects 
Next, we will create an example database, schema and table with the following code:

```sql
create or replace database MARKETING;

create or replace schema MARKETING.WEBSITE;

create or replace table MARKETING.WEBSITE.EVENTS ( 
    id string,
    event_type string,
    event_page string,
    user_name string,
    user_score int,
    event_timestamp timestamp
);
```

Click `Databases` to review your newly created database, schema and table. Hint: you may have to refresh your browser window.

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
    auto_resume = TRUE
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
grant ownership on table MARKETING.WEBSITE.EVENTS to role DATA_ENGINEER;
grant select on table MARKETING.WEBSITE.EVENTS to role DATA_ANALYST;
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

Click on `Databases > MARKETING > WEBSITE > Tables > EVENTS` to review your changes.

Try to figure out if there is another way to use the DATA_ENGINEER role with the ENGINEER_WH.

Try to perform the same alteration with the DATA_ANALYST role. Notice how you are not able to change the table because you have insufficient permissions.