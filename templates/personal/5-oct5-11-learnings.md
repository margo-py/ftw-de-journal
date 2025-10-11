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



# Intro to OOP
## Inheritance 
- Creating a subclass 
  - Subclasses inherit attributes from parent class
  - Problem: Create a Manager class inherited from Employee class
    ```python
      class Employee:
        MIN_SALARY = 30000    

        def __init__(self, name, salary=MIN_SALARY):
            self.name = name
            if salary >= Employee.MIN_SALARY:
                self.salary = salary
            else:
                self.salary = Employee.MIN_SALARY
            
        def give_raise(self, amount):
            self.salary += amount      
            
    # Define a new class Manager inheriting from Employee
    class Manager(Employee):
        # Add a keyword to leave this class empty
        pass

    # Define a Manager object
    mng = Manager('Debbie Lashko',86500)

    # Print mng's name
    print(mng.name)
    ```
  
  - All or nothing when creating subclass inheritance. All objects are passed.

  ## Creating a customized subclass


# Oct 11 Personal notes
7:32am @APC Cafeteria
- I know that this time I will definitely sleep again because I had no sleep last night
Sleep defenses
- drink water 
- eat snack
- drink hot milk 11:22
- ointment
- tell seatmate you're sleepy
- sit upright / posture
- put water on face
- deep breaths
- Keyword Game: Pick a keyword (e.g., “process”) — tally how often it’s said.
- remove socks 🧦
UPDATE: EATING GUM HELPS; I WAS ABLE TO POWER THROUGH HAVING 0 SLEEP FOR 24 HRS DUE TO WORK



9:14am
# Data Collection & Web Scraping
- hardest
- Sir Myk is trying to make sure we don't use web Scraping 
- Day 5 is skills for capstone Web scraping 101
- FTW IS NOT SPOONFEED
- The reality is the requirements in job is too varied. But we're trying to pin point is the common. You need to polish

# Lesson Proper

- Agenda For Today
  - Data In The Wild
  Collecting and Handling Data: From Representation to Real-World Responsibility 
  - Data works in a lifecycle 
  1. something happening that generates data (Event)
  2. That something is represented by something else -> temperature -> why is there a terminology for tem?
    - Sun, phenomenon, and we wanted to measure it, that's where the temperature is
  3. Sensor
    - Some sort of measure, represent into a number
    - question, urgency, importance (pwede naman wala ka paki sa temperature)
      - you want to measure with trend/threshold
      - Sir myk 24-26 is the ideal
        - action is to adjdust the tempeaature
    - Earthquake detection for Davao
      - they have sensors and they capture info

  ## 📊 Data as different shapes
  - Structure
    - Tabular -> sales table -> csv / sql
    - Hierarchical -> product catalog -> json/xml
    - graph -> think in relationships -> neoj4
    - Time series -> sensor readings -> parquet/influx
    - Geospatial -> map coord -> GeoJSON, Shapefile 
      - maps arent flat 
        - has layers, elevation
        - satellite data

    Myk: mas marami skillset na kailangan kapag multimedia databases 
    - in practice, mostly structured data in the phase
    - philsa / solar -> pag mga power, time series dataset, social 
    - konti multimedia databases


  ## 🔢 Data Types and Semantics
    - primitive
    - temporal
    - categorical -> enums, codes, labels, 
      - depends on database
        - ex. tableau / powerbi
          - specify modeling instead of categorical 
            - location, country, states, cities
            - date paano irerepresent? gagamit multiple integer, year, month, 
            - if you have datestamp, 24 hours or 12 hours (flag for morning or afternoon)
              - need may checks. pag nagpasok more than 12 hrs, dapat magfafail.
              - why respresentation different vs data type?
              - string is heavy
              - 2020-07-23-13-58-29 -> string, a character is ASCII. 
                - ASCII very basic way to represent characters
                - reason for varchar 262 varchar 255
        - efficiency formats 
          - csv human-readable but large
          - json - flexible, nested (dictionary, key value pairs)
            - moving data around in the web
          ![](https://nexla.com/n3x_ctx/uploads/2018/05/Avro-vs.-Parquet-1.png)
          - UNKNOWN UNLESS U GET GET IN THE GIGABYTES
            - Data Analysts mas marami -> okay lang puro csv
              - They will hire you because they have a problem
              - fofcus lagi is the flow of the data, pipeline palagi
                - HOW DO WE BUILD THE PIPELINE?
                 - FOCUS ON THE FLOW ITS EASIER, NOT THE TOOLS
                 - engineers -> building things to last.
                  - automate on its own, it's reslient


  ## Advanced data Formats for analytics
  - HDF5
  - Parquet
  - ORC


  Mika i think I know what my problem is with less confidence in math and logic vs creativity
  I respect logic and maths a lot. I know I can be creative and being in this industry means I get to be free 
  Mistakes are allowed
  I feel like in logic-oriented jobs, it isn'them.
  But overtime, learning about Data Engineering made me realize that it's okay to make Mistakes
  In fact, it's the way people learn!


  # Financial Literacy
  - Life Insurance
   - DIC SEC Governing bodies, dont take life insurance less than the estimated retirement year
  

  # Web API Ingestion Afternoon Session
  - venv
    - run only inside the environment
    - Sir myk thinks UV best package manager now
  
  # Files - Pandas is your BFF
  - never nakuha ni sir data engineer pero never fgamit sql and python 
  - da - statistics, de - data modeling (underlying)
  - sql will win 
  - python ingest data, convert any source to a csv to csv -> pipe it to dlt 
  - pandas inefficient -> GB space x not pandas 
    - for most 80% problems okay lang pandas%
    - more complex, there are better tools 
    - pandas marami built in stuff
    2:38pm di inantok
      1. nagpunta 7th float
      2. kumakain ng gulay
    User Guide or API Reference
    ![](https://i.imgur.com/95uPXNt.png)

  # Connecting to databases
  - Open 

  https://pandas.pydata.org/docs/user_guide/io.html

  - 60 - 70% jSON Related cases %
    - appreciat na 'ah' pwede pala yun?!
    - save as csv writer to_csv
    ![](https://i.imgur.com/ECxnbkR.png)
    - when are the times u dont like to have csv as your file -> when address data
    - that comma is a separator so -> if address / creative parents with comma name
    - labo labo columns pag silip sa data may labo labo palang columns, may spaces, comma etc 
      : “X Æ A-12 Musk.” 
    - 30-50% data requirements will be covered by this. others are json paano pag nested? diff problem, di natin covered
    - another if it is 
    - The way panda work it loads on memory -> pandas inefficent 
    - Sir myk's suggestion -> use this in your capstone

  # Connecting to databases
  - SQL is your foundation
  - Extract small slices, not whole database
  - Use LIMIT, filters, or updated_At timestamps
  - Validate row counts, schemas

  Ibis - An opensource data frame libraray that works with any data system
    - Use the same API for nearly 20 backends
    - Use local dataframes with embedded duckdb debafault polrars or data fuiosnn
    - 50 to 40 percent pwede mag source 
    - https://ibis-project.org/
    💡 Think:
        Use pandas for small local data.
        Use ibis for large or remote database data — same feel as pandas, but runs efficiently in SQL backends.

  
  # API
  - Can mean different things
  - pandas, import it as part of a package
    - interact and use other software as part of another software
    - API Handbook of a car 🚗
      - basta pag ni run produce csv (idea around application interface )
        - abstraction
        - abstacted nitty-gritty
  
  # APIs: The Modern Data source
  - RESTAPI - Machines talking to Machines
    -REST:GET,POST,PUT
    -Responses often in JSON
    -Handle rate limits & Authentication

  # Understanding APIs and how to read them
  Key concept -> Endpoint yung pinaka BABANTAYAN
    - need to pass a command here
    - Credential -> learn how to read API REST API ENDPOINTS
    - Retrieving data from the internet (REST API)
    - OVERVIEW AUTHENTICATION 
    - https://open-meteo.com/
    - favorite place ka, first place niyo nag meet, kung mainit ba dun

  # Practical API tips 
  - paginate results (next, offset)
  - Respect rate limits
  - Retry with backoff
  - Log every request (timestamp, duration, etc)
    - kung kaya mag log ng request better
    - http error codes 
      - rest api works through http status codes


  # Exercise
  Get temperature of manila
  ![](https://i.imgur.com/7zu5ECN.png)
  ![](https://i.imgur.com/w8ZW6Hg.png)
  - json has a page within a book

  # Topic Undiscussed
  - API (ftw-de-bootcamp)
    - you can explore 
    - meteo, wmo-codes 
    - btc -> only captures 1 instance cron job (every 5 min pull data from bitcoin and )

  # Web Scraping
  - kung kaya wag gawin web scraping in capstone, yun gawin
  - API skills - use to web scrape
  - Websites dont move but as you scroll down, it updates. If that, they have api sources in the back (fb) you can try getting data that
    - easier to process json file than web scraping 
    [ REMEMBER IF ANYONE IS BETTER THAN YOU, AND FOR SURE THERE WILL, THEY WILL BE HELPFUL! NOT A THREAT!] 
  - if you can use Edge, chrome, and you can copy-paste it, you can automate it usinig software. 
    - not that easy, because websites counter it 
      - grey area in web scraping
      - any data in public is scrapable
      - form of copying

  ## How websites work
      SERVER -> Client (chrome) connects to server
        chrome: pahingi ng page
        server -> sends back an html file
      BROWSERS Work -> theory is ang browser kaya niyang iinterpret and idisplay dapat pwede tayo gumawa ng application that acts like a browser, 
        insteaed of printing the website, we just get the data that we want. The problem is that there is no standard way of designing websites/ The way websites done today are labo labo
        - WEBSITES X DIFFERENT WEBSITES
        - Totally different
        HTML -> IMAGE A TREE 🌳 
          - Parse TREE
          - DOM (Document object model)

  # HTML The Foundation of Every Web page (3:53PM)
  - HTML -> as long as mahanap mo yung mga <a> hyperlinks 
    - you can get the web scrape. (webscraping)
    - go to the root of website -> website ni shie, kuhanin hyperlink 
    - link tree network
    - in our case, there are websites out there wikipedia, so many tables, extract, put into a csv file
    - library BEAUTIFUL SOUP 
      -LIBRARY FOCUSES ON DOM -> PARSE TREE
        - what you want is to zoom in on the objects to look at the links 
        https://github.com/wention/BeautifulSoup4
        ![](https://imgur.com/32653a97-f80e-4ea0-8eb2-82211392dc7b)

  # JavaScript: The Hidden Layer that Makes Pages Dynamic
  - api detect in browser
  - grey area -> merong api sa lazada but it's hidden (part of operations)


  ### Sample Lazada
  -> REQUEST URL HEADERSTAILNODE NA GINAMIT
  ![](https://i.imgur.com/yZPEHL2.png)


   - create our own browser, click browse, then we scrape
  ## 2. Use a headless browser



  # Levels of Web Scraping Difficulty
  1. Basic HTML static 
  2. Intermediate Dynamic / Javascript-rendered
  3. Advanced Protected (captcha, facebook, )
    - partner with the company effor alone is very heavy than partnering with an external company.

  # Web Scraping Decision tree
  - Static -> Beautiful SOUP
    - use requests + based
    - the data is already in the page source (you can view page source and see it)
  - Dynamic - API Endpoint 
    - check if there's an api Endpoint
    - inspect network xhr in devtools to find json data calculations

  - No API
    - headless browser
    https://quotes.toscrape.com/tag/love/
    ![](https://i.imgur.com/HSNslNf.png)
    ![](https://i.imgur.com/WUac6tS.png)

  - playwright
    - headless (not going to create a separate browser)
    - it will try to scrape resulting html
    - has its own selection method to model objects in the webstel 
    - chromium download headless browser

  # Data Ethics
  - Closing Topics
    - largely unseen, but largely critical. My goal is to make it seen. To champion the importance of the role. Doesn't we can collect to collect all. Doesn't mean we share is we share all.
    - Influenced by business 
    - Part of our role to advice them
    - Find balance around it. 
  1. Collection is everywhere
  2. Consent and Awareness Matter
  3. Beyond the Technical 'How' - The Ethical 'should'
  4. Impact on Privacy, Safety, and Dignity
  5. Responsiblity as Data Practictioners

  # How do we use ethics when dealing with data
    - data is a shared respoonsbility 
    - someone out there creates the problem statements
    - revolves around the process 
    - minimize data
    - no need to collect everything
    - seek informed consent
    - maintain data quality
    - be transparent

  # Ways to avoid common data ethics Mistakes
    - lack of transparency
    - ignoring bias 
    - data is always biased -> the data they callect. bias gives them sharp power over the Collection
      - if you don't know bias such that you would know the gaps of that bias
      - not always obvious 
      - part of the journey

  # Real World Tips for Beginner Data engineers
  1. Continous Learning
  2. Building a portfolio
  3. Finding jobs
  4. Handling Interviews
  5. Building a career


  FTW BOOTCAMP 5 - SCRAPY -> FRIEND OF PLAYWRIGHT FOR MORE COMPLEX 
  - LAZADA (5 PAGES OF LAZADA PRODUCTS -> DLT CONNECTED)
  - use dbeaver to import data- 