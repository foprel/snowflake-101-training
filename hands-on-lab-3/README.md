# Hands-on lab 3

## Goal
The goal of this third hands-on lab is to familiarize you with some of Snowflake's unique features. In this lab you will learn how to use zero-copy cloning to replicate data and to apply data masking to shield sensitive information.

### 1. Create dummy data
We will create some dummy data for the purposes of the next excercises. You can use your previously created DATA_ENGINEER role and ENGINEER_WH for this:

```sql
-- Select role
use role DATA_ENGINEER;

-- Select warehouse
use warehouse ENGINEER_WH;

-- Insert values
insert into MARKETING.WEBSITE.EVENTS
  values
  ('1', 'click', '/home', 'Frank', 10, to_timestamp('2013-05-08T23:39:20.123')),
  ('2', 'click', '/work', 'Daan', 10, to_timestamp('2013-05-08T23:39:20.123')),
  ('3', 'click', '/blog', 'Jeannine', 10, to_timestamp('2013-05-08T23:39:20.123'));
```

### 2. Create and apply masking policies
Snowflake allows you to easily mask data for specific user roles. Use the following code to mask data for all roles except the DATAANALYST and DATAENGINEER roles.


```sql
-- Use database
use database MARKETING;

-- Use schema
use schema MARKETING.WEBSITE;

-- Create masking policy for strings
create or replace masking policy simple_mask_string as (val string) returns string ->
    case
        when current_role() in ('DATA_ENGINEER') then val
        else '*** masked ***'
    end;
  
-- Create masking policy for integers
create or replace masking policy simple_mask_int as (val integer) returns integer ->
    case
        when current_role() in ('DATA_ENGINEER') then val
        else -999
    end;
```

Apply the masking policy to columns of the EVENTS table:

```sql
alter table MARKETING.WEBSITE.EVENTS modify
    user_name set masking policy simple_mask_string,
    user_score set masking policy simple_mask_int;
```

Try to switch to the DATA_ANALYST role and select data from the EVENTS table:

```sql
grant select on table MARKETING.WEBSITE.EVENTS to role DATA_ANALYST;
use role DATA_ANALYST;
use warehouse ANALYST_WH;
select * from MARKETING.WEBSITE.EVENTS;    
```

Can you still see all the data? What happens if you try the same with the ACCOUNTADMIN role?

### 3. Zero-copy clone the table
Let's create a simple development environment based on the EVENTS table to see how zero-copy cloning works in practice.

```sql
create or replace MARKETING.WEBSITE.DEV_EVENTS
    clone MARKETING.WEBSITE.EVENTS;
```

Notice how data is still masked:

<img src="https://github.com/foprel/snowflake-101-training/blob/main/images/zero-copy-clone.png" width="400">


