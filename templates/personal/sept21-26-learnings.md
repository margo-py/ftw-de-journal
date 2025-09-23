# September 21-26, 2025 Learnings
# 📑 Table of Contents
- [September 21-26, 2025 Learnings](#september-21-26-2025-learnings)  
  - [📊 Datacamp](#-datacamp)  
    - [Intro to Relational Databases in SQL](#intro-to-relational-databases-in-sql)  
    - [Model 1:N relationships with foreign keys](#model-1n-relationships-with-foreign-keys)  
    - [Exploring London's Travel Network](#exploring-londons-travel-network) 
    - [DSA In Python](#dsa-in-python)
      - [Chapter 1](#chapter-1)
        - [🧷 Linked list](#-linked-list)
        - [Big O Notation](#big-o-notation)
        - [Stacks](#stacks)
      - [Chapter 2](#chapter-2)
        - [Queues, Hash Tables, Trees, Graphs, and Recursion](#queues-hash-tables-trees-graphs-and-recursion)
          - [KYUS (Queues)](#kyus-queues)
          - [Hash table](#hash-table)
          - [Trees](#trees)
          - [Graphs](#graphs)
          - [Recursion](#recursion)
      - [Chapter 3](#chapter-3)
        - [Searching algorithms](#searching-algorithms)
          - [Linear Search and Binary Search](#1️⃣-linear-search-and-binary-search)
          - [Binary Search Tree (BST)](#binary-search-tree-bst)
      - [Chapter 4](#chapter-4)
        - [Sorting algorithms](#sorting-algorithms)
    - [🐍 Writing Efficient Python](#-writing-efficient-python)
    - [🧪 Intermediate DBT](#-intermediate-dbt)

## 📊 Datacamp
### Intro to Relational Databases in SQL
(Timo Grossenbacher - Journalist and Data Scientist)
- The not-null and unique constraints
    - NULL
        - Doesnt exist
        - Value doesnt apply to the column
    - UNIQUE Constraint

Chapter 3
- Keys and Super Keys
    - Super key - candidate key
    - Super Key vs Candidate Key
![Image](https://media.geeksforgeeks.org/wp-content/uploads/20230314093236/keys-in-dbms.jpg)
- Primary Key

#### Model 1:N relationships with foreign keys
- Relationship types
    - small numbers represent cardinality
    - Foreign key - points to primary key in another table (referential integrity)
    - FK - not actual keys, duplicates are allowed 
- Model complex relaetionships
 - Main Goal: Learn to model and implement database relationships (1:N and N:M) using tables and foreign keys.
 - Referential Integrity
    - always concerns two talbes
    - enforced through foreign keys 


### Exploring London's Travel Network
- Answer the ff.
1. Most Popular Transport Types 
    - Measured by 
        - 1.1 `journey_type` total number of journeys
        - 1.2 `total_journeys_millions`
    - Sort 2nd column desc
    - Save as `most_popular_transport_types

2.   Five months and years most popular for the Emirates Airline
    - Return `month`, `year`, `journeys_millions` -> aliased as `rounded_journeys_millions`, round 2 decimal
    - exclude null

3. Lowest volume of underground & dlr journeys alias as `least_popular_years_tube`
    - results should contain cols `year`, `journey_type` and `total_journeys_millions`

Select data using the syntax FROM `TFL.JOURNEYS`


### 🐍 DSA In Python
### Chapter 1
#### 🧷 Linked list
   - data 
   - pointer

   ##### Python study
   - Class `class ClassName` -> class + CamelCase
   - `__init__` -> constructor

   ##### Instanstiate:
   ```
   class Node:
  def __init__(self, data):
    self.value = data
    # Leave the node initially without a next value
    self.next = None

   class LinkedList:
   def __init__(self):
      # Set the head and the tail with null values
      self.head = None
      self.tail = None
   ```

   ##### 📩 Insert node at beginning of linked list
   ```
   def insert_at_beginning(self, data):
    # Create the new node
    new_node = Node(data)
    # Check whether the linked list has a head node
    if self.head:
      # Point the next node of the new node to the head
      new_node.next = self.head
      self.head = new_node
    else:
      self.tail = new_node      
      self.head = new_node
   ```

   ##### 📩 Removing node at beginning of linked list
   ```
   class LinkedList:
   def __init__(self):
      self.head = None
      self.tail = None
      
   def remove_at_beginning(self):
      # The "next" node of the head becomes the new head node
      self.head = self.head.next
   ```
   #### Big O Notation
      - worst case complexity of algorithm
         
         **Time Complexity**
         - Time complexity is the reason big data is even possible.

         Space complexity
         - Without efficient algorithms, handling millions or billions of rows would be too slow to be useful.
      ##### O1 O(n) Linear
      ##### O(n^2) Quadratic
      ##### O(n^3) Cubic
      - Print all elements in the list 
      `colors = ['green', 'yellow', 'blue', 'pink']`
         - O(n) complexity
         - O(n) vs O(1) 
            - 1 - constant time 👉 Example: checking the first element of a list.
               - Popping stacks
            - n - linear 👉 Example: printing every element of a list.


   #### Stacks
      - Last in first out
      - Tower of Hanoi
         - Push add to the stack
         - Pop remove from the stack 
         - Peek read the stack
      - LifoQueue 
         - behaves like a stack
      - O(1) Popping stacks
      - LifoQueue
      `import LifoQueue()`

Importance of DSA
- Heaps
- Hash table, hashing -> quick lookups
- Searching
   Stacks 
   - Processing data streams, help reverse sequences efficiently

### Chapter 2
#### Queues, Hash Tables, Trees, Graphs, and Recursion
   ##### KYUS  (Queues)
   - Enkyu (Enqueue)
   - Dikyu (Dequeue)
![DQ EQ](https://media.geeksforgeeks.org/wp-content/uploads/20220816162225/Queue.png)
   - Printer queue 🖨️
   The syntax is basically **dot chaining**: `self.queue.dequeue()`
      ```
      object.method()
      object.attribute
      object.attribute.method()
      ```
   - Python's SimpleQueue "IMPORT QUEUE" 
      ``` 
      import queue

      # Create the queue
      my_orders_queue = queue.SimpleQueue()

      # Add an element to the queue
      my_orders_queue.put("samosas")

      # Remove an element from the queue
      my_orders_queue.get()
      ```

   ##### Hash table
   - key value pairs
   - python dictionaries
      - Get
      - Insert
      - Modify
      - delete
      - clear
      - loop / iterate
      - nested
   ``` 
   my_menu = {
   'lasagna': 14.75,
   'moussaka': 21.15,
   'sushi': 16.05,
   'paella': 21,
   'samosas': 14
   }
   # Correct the mistake
   for key, value in my_menu.items():
   # Correct the mistake
   print(f"The price of the {key} is {value}.")
  ```

   ##### Trees
   Node - based 
   - binary tree
   - Searching and sorting
   - vertices, edges

      ##### Graphs
      - formed by set of nodes (vertices)
      - connected by links (edges)
         - undirected (no direction 🚫)
      - Weighted Graphs
         directed or undirected
         - A weighted graph is a graph where each edge is assigned a numerical value, or "weight," representing a cost, distance, or capacity
         - can have unconnected nodes (🌴 trees must always have connect)
      - Graphs (social networks, location)
      
      UNWEIGHTED GRAPH
      ```
      class UnweightedGraph:
      def __init__(self):
         self.vertices = {}

      def add_vertex(self, vertex):
         self.vertices[vertex] = []

      def add_edge(self, source, target):
         self.vertices[source].append(target)

      ex:
      my_graph = WeightedGraph()

      # Create the vertices
      my_graph.add_vertex('Paris')
      my_graph.add_vertex('Toulouse')
      my_graph.add_vertex('Biarritz')

      # Create the edges
      my_graph.add_edge('Paris', 'Toulouse', 678)
      my_graph.add_edge('Toulouse', 'Biarritz', 312)
      my_graph.add_edge('Biarritz', 'Paris', 783)

      ```

      ##### Recursion
      - returns itself
      - avoid infinte rekursion (Base case)

         ##### Dynamic Programming
         - optimization technique
         - reduce complexity of recursive algorithms 

      In Tower of Hanoi:
      - source (from_rod) = where the disks start.
      - target (to_rod) = where you want to move all disks in the end.
      - auxiliary (aux_rod) = a helper rod used as temporary storage.



### Chapter 3
#### Searching algorithms
   ##### 1️⃣ Linear Search and Binary Search
   **Linear**
   - O(n)

   **Binary**
   - O(log n)
   - ordered list only (DAPAT ORDERD OK KASI KINAKALAHATI)
   - 👉❌ If you run binary search on an unordered list, the result is wrong / unpredictable.

   ##### Binary Search Tree (BST) 
   - FAV 💜
   - Left subtree of a node:values less than the node itself
   - Right subtree of a node:values greater than the node itself
   - Left and right subtrees must be binarysearch trees
   - Deleting
   - Faster at searching vs Arrays/linkedlists
      ##### Inserting a node BTS
      ```
      bst = CreateTree()
      bst.insert("Pride and Prejudice")
      print(search(bst, "Pride and Prejudice"))
      ```
   - Finding the minimum node of a BST
   - Depth First Search (DFS) - Traversal, visiting all nodes
      - In order (left, current, right)
      ![Inorder](https://skilled.dev/images/in-order-traversal.gif)

      - Pre order (current -> left -> right)
      ![Pre order](https://skilled.dev/images/pre-order-traversal.gif)




### Chapter 4 
#### Sorting algorithms



### 🐍 Writing Efficient Python
####  ![Python Rules](https://hanzhu.dev/content/images/size/w1200/2019/03/zen_of_python_poster_by_ewjoachim-d6kg2kb.png)





### 🧪 Intermediate DBT
- dbt test
   `model_properties.yml`
- Finding bad data via Data Validation 
- Create Singular Data Test
   - sql query in test directories
   - custom data test
   - Singular test with Jinja 
- Reusable tests `tests/generic`