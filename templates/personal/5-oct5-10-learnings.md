# Oct 5-11, 2025 Learnings

Learning stuff based on what I have learned from before helps me remember better.
This personal journal entry will be like an easy-access summary I can look up for myself by using the search button if I ever forget concepts. 
These notes are what I personally took from lessons.

## Parallel Computing
- sounds hard to me. 
- Sounds like Quantum Computing.
- Strategically divide tasks to finish it faster.
- Like dividing data cleaning tasks to grpmates.
  - But instead of dividing, I did the task alone.
- Now that I broke it down, I am able to understand it. 
    - Learned **Communication Overhead**.
        - this happens when too much talking, waiting, chikka is happening.
            - I made ai create a meme for this.
            - I noticed that the link is so long, and I wondered how much links are made to cater the needs of many people in the world.


### Frameworks
- Hadoop  
  HDFS -> aws s3 replace this  
- MapReduce  
- Hive  
- Spark  
  - RDD resilient distributed datasets  
  - Resilient Distributed Dataset  
- Pyspark  

## Workflow scheduling Frameworks
- Dbeaver is called SQL Client  

  A SQL client is a software application or component that allows users or other applications to connect to and interact with a SQL database server. It acts as the "client" in a client-server architecture, sending requests (SQL queries) to the "server" (the database management system) and receiving responses (query results) back.

  **Key aspects of a SQL client:**
  - Connection Management: It establishes and manages connections to the database server, handling authentication and network communication.
  - Query Execution: It enables users to write and execute SQL statements (e.g., SELECT, INSERT, UPDATE, DELETE) against the database.
  - Data Visualization and Management: Many SQL clients provide graphical interfaces for viewing, editing, and managing database objects like tables, views, and stored procedures.

- How to schedule  
  - manually - doesn’t scale well  
  - cron scheduling tool (Linux)  
  - what about dependencies  

- Directed Acyclic graph  
  - not part of a cycle, no closing (like alcohol chemistry ^^^)  
  - CRON linux  
  - Spotify Luigi  
  - Airbnb  
  - directed edges ->  

- Airflow  
  - Project manager of data pipelines  
  - Airbnb  
  - I've heard of Airtable before, I wondered if Airflow is related to Airtable but no  

- Spark  
- Spotify Luigi  
  https://github.com/spotify/luigi  
  https://engineering.atspotify.com/  

- Exercises  
  - remember that N represents numbers  

# ETL
- Extract Transform Load  
- Persistent storage  
- JSON  
- Web  
  - requests data on web  
  - request gets responses  
  - many web servers use JSON to communicate with web data  

```python
import requests
response = requests.get("link.json")
print(response.json())
```
## Load
```python
Here’s the core takeaway 👇

You connect to Postgres using a URI →
postgresql://user:password@host:port/database

You write data from pandas to SQL with →
df.to_sql("table_name", engine, schema="schema", if_exists="replace")

Main idea:
You’re moving transformed data (from Python) → into a database (Postgres)
so apps or BI tools can use it.

That’s the important part — you’re learning how to load final cleaned data into a warehouse.
```
Introduction to PySpark — to learn Spark from Python. 
https://www.datacamp.com/courses/introduction-to-pyspark?utm_source=chatgpt.com

## Workflow Airflow
- BashOperator
- cron expression `"0 * * * *"`

# Final Chapter
## Datacamp course ratings (Mika wrote this to better understand)
- In chapter 4, the goal here is to understand the past 3 chapters by doing ETL on datacamp dataset 
    - transform raw -> rating data -> actionable course recommendations for datacamp students
    1. explore using schema 
        - top rated courses good to recommend to people 
        - people who rated python as high == more python recommendations    
        - extract data, transform = clean, calculate, recalculate daily, load to database
        - show user's dashboard 
    2. query the table 
        ```python
        # Complete the connection URI
        connection_uri = "postgresql://repl:password@localhost:5432/datacamp_application" 
        db_engine = sqlalchemy.create_engine(connection_uri)

        # Get user with id 4387
        user1 = pd.read_sql("SELECT * FROM rating WHERE user_id=4387", db_engine)

        # Get user with id 18163
        user2 = pd.read_sql("SELECT * FROM rating WHERE user_id=18163", db_engine)

        # Get user with id 8770
        user3 = pd.read_sql("SELECT * FROM rating WHERE user_id=8770", db_engine)

        # Use the helper function to compare the 3 users
        print_user_comparison(user1, user2, user3)
        ```
    3. average rating per course 
        - Action tasks // next time, search the syntax. this is the thought process.
        - Identified what to do to to solve the main problem:
            - get top rated courses from the dataset
                - to get this, getting the average rating per course is needed. why? to sort the total averages and rank the courses by rating.
            - to get the average here are the steps: 
                - create transformation function `transform_avg_rating`
                ```python
                def transform_avg_rating(rating_data):
                    # on raw, grouped it by course_id to collate all same courses. then get the mean of all of them.
                    avg_rating = rating_data.groupby('course_id').rating.mean()
                    sort_rating = avg_rating.sort_values(ascending=False).reset_index()
                    return sort_rating

                # Extract the rating data into a DataFrame    
                rating_data = extract_rating_data(db_engines)

                # Use transform_avg_rating on the extracted data and print results
                avg_rating_data = transform_avg_rating(rating_data)
                print(avg_rating_data)
                ```
    4. recommendation table
        - a. clean null first and corrupt data
            - datacamp recommended the 'Building Recommendation Engines with PySpark'
            - (Your thoughts have value. first ask yourself whats wrong or what the problem is looking for based on what you would say)
            - instuctions are to print sum of all null rows
            - then create function to turn all nulls to R
            - then print the number of missing cols in transformed 
                ```python
                course_data = extract_course_data(db_engines)

                # Print out the number of missing values per column
                print(course_data.isnull().sum())

                # The transformation should fill in the missing values
                def transform_fill_programming_language(course_data):
                    imputed = course_data.fillna({"programming_language": "R"})
                    return imputed

                transformed = transform_fill_programming_language(course_data)

                # Print out the number of missing values per column of transformed
                print(transformed.isnull().sum())
                ```
        - b. actual recommender system
            ```python
            # Complete the transformation function
            def transform_recommendations(avg_course_ratings, courses_to_recommend):
                # Merge both DataFrames
                merged = courses_to_recommend.merge(avg_course_ratings) 
                # Sort values by rating and group by user_id
                grouped = merged.sort_values("rating", ascending=False).groupby("user_id")
                # Produce the top 3 values and sort by user_id
                recommendations = grouped.head(3).sort_values("user_id").reset_index()
                final_recommendations = recommendations[["user_id", "course_id","rating"]]
                # Return final recommendations
                return final_recommendations

            # Use the function with the predefined DataFrame objects
            recommendations = transform_recommendations(avg_course_ratings, courses_to_recommend)

            ```
    5. scheduling daily jobs 
        - use the calculations made in 1-4
        - schedule using airflow 
        - loading phase 
        - create the DAG
            - get target table
            ```python
            def load_to_dwh(recommendations):
                recommendations.to_sql("recommendations", db_engine, if_exists="replace")
            ```
            - define DAG (uses cron notation on schedule interval)
            ```python
            # Define the DAG so it runs on a daily basis
            dag = DAG(dag_id="recommendations",
                    schedule_interval="0 0 * * *")

            # Make sure `etl()` is called in the operator. Pass the correct kwargs.
            task_recommendations = PythonOperator(
                task_id="recommendations_task",
                python_callable=etl,
                op_kwargs={"db_engines": db_engines},
            )
            ```
            ```
                        * * * * *  
            │ │ │ │ │  
            │ │ │ │ └── Day of week (0–6, Sun=0)  
            │ │ │ └──── Month (1–12)  
            │ │ └────── Day of month (1–31)  
            │ └──────── Hour (0–23)  
            └────────── Minute (0–59)
            ```
    6. querying the recommendations
    - im thinking if im going to do this with a group, proj management 1st (2hrs comm overhead)
        + actual task division
        + skill assessment
        ```python
        def recommendations_for_user(user_id, threshold=4.5):
            # Join with the courses table
            query = """
            SELECT title, rating FROM recommendations
            INNER JOIN courses ON courses.course_id = recommendations.course_id
            WHERE user_id=%(user_id)s AND rating>%(threshold)s
            ORDER BY rating DESC
            """
            # Add the threshold parameter
            predictions_df = pd.read_sql(query, db_engine, params={"user_id": user_id,
                                                                "threshold": threshold})
            return predictions_df.title.values

        # Try the function you created
        print(recommendations_for_user(12, 4.65))
        ```
    - Good job! This was the final exercise of this course. By using your skills as a data engineer, you're now able to build intelligent products like recommendation engines.
