# September 21-26, 2025 Learnings

Learnings
- SQL is declarative, not procedural.



### 📊 SQL Datacamp

#### Database Design
- Query in snowflake schemas - It is challenging to drill-down but it was also fun. I needed to analyze what similar fields are in the connected tables to find out the specific data that is being asked.
<img src="https://i.imgur.com/vYPLPHB.png?1[/img]" height="450" width="800">

- ADD CONSTRAINT - foreign keys constraint
- Views x physical table
- Airflow - pipeline schedulers
- Materialized View
- **Partitioning**
   - Vertical Partitioning
      - link via shared key
   - Horizontal Partitioning
      - split rows (ex. q1 q2 q3 q4 timestamp)
```CREATE TABLE film_2019 PARTITION OF film_partitioned FOR VALUES IN ('2019');```

#### Snowflake SQL - flavor of SQL
   - INITCAP()
   - CONCAT
   - CAST(col_name AS data_type)
   - EXTRACT(_ FROM _) = EXTRACT(weekday FROM date_column)
   - CURRENT_DATE, CURRENT_TIME
   - Uncorrelated Subquery
   - Correlated Subquery 
      - Common Table Expressions (CTE) -> "with" keyword
         - Liek giving a nickname to a sub query 
         [![Subquery meme](https://miro.medium.com/v2/resize:fit:1400/1*DxMMEBw11hFYGLpqHuVU9Q.jpeg)
   - Snowflake Query Optimization
      - Exploding Joins
   - Snowflake JSON
      - object_construct
      - parse_json
      - colon/dot notation

#### Joining SQL
![Merge](https://media1.tenor.com/m/cq6PaHOrRpwAAAAC/dragon-ball-z-fusion.gif)

**Outer Joins, Cross Joins and Self Joins**
- X Join
   - all combinations (for example all possible languages being used by two countries if they are close with each other)
- INNER JOIN
   - exact matches only 
   - shrinks results
- LEFT JOIN
   - Need all from left table even if there's NULL in right table.
   - expands results
- SELF JOIN
   - aliasing required
   - <> sql not equal operator

**Set Theory / Set Operations**
- Union
   - x stacks, x duplicates :(
- Union All
   - / stacks, / duplicates :)
- INTERSECT (row records common to both tables, EXACT match in everey field value)
   - INTERSECT vs INNER JOIN
      - INNER JOIN, same in code only, not in other field values.
- EXCEPT
   - same records present in left, not in right

** Subquerying / Nested **
- Additive Joins
   - Semi join
   - Anti join
      - joins records no match col 1 to col 2
      - `NOT IN` statement
- Subqueries inside WHERE, SELECT, FROM
   - Filtering is one of the most common data manipulation tasks
   - Subqueries INSIDE SELECT
- Subq FROM
   
**Intermediate DBT**
- Test
   - Assertions
- Apply tests in modes/model_properties.yml

DBT
- dbt run
- ./datacheck - not part of dbt, but used to check cols
- dbt docs -h
- dbt docs generate
    Creates the documentation website based on project
    Should be run after dbt run
- dbt docs serveShould only be used locally / fordevelopmen
- jinja
**DBT MODEL** 

To learn:
- How to use foreign keys & alter
