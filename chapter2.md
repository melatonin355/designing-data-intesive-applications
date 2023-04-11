# Chapter 2

Data Models and Query Languages

The limits of my language mean the limits of my world.
—Ludwig Wittgenstein, Tractatus Logico-Philosophicus (1922)


Chapter 2 focuses on data models and query languages, which serve as the foundation
for understanding how various databases and systems are designed to store, organize, and retrieve data.

## Data Models:
Data models are abstract representations of how data is organized and structured. They are essential for ensuring that data is stored efficiently and can be easily accessed and manipulated. The chapter discusses three primary data models:

a. Relational Model: Introduced by E.F. Codd in 1970, the relational model organizes data in tables (relations) consisting of rows (tuples) and columns (attributes). It is the basis for SQL databases, which are widely used in various applications.

b. Document Model: This model stores data in semi-structured formats like JSON or XML. It is schema-less, allowing more flexibility in data representation and is often used in NoSQL databases like MongoDB and Couchbase.

c. Graph Model: The graph model represents data as nodes (entities) and edges (relationships). It is particularly suitable for representing complex relationships, such as social networks or recommendation systems. Graph databases like Neo4j and ArangoDB utilize this model.


## Query Languages: 

Query languages are used to interact with databases and retrieve or manipulate data. They help bridge the gap between human-readable queries and low-level data storage. The chapter discusses several query languages for different data models:

a. SQL (Structured Query Language): SQL is a declarative query language used primarily for relational databases. It allows users to specify what data they want, rather than how to retrieve it.


b. MapReduce: A functional programming model used for processing large-scale data across distributed systems. It involves two primary functions - Map (transforming data) and Reduce (aggregating results).

c. NoSQL Query Languages: Various NoSQL databases have their query languages, such as MongoDB's JSON-based query language or Cypher for Neo4j.

d. GraphQL: A query language for APIs, allowing clients to request specific data from a server. It enables more efficient and flexible data retrieval.

## Relational Model Versus Document Model

the section on the relational model versus the document model highlights the fundamental differences between these two data models and their respective use cases.

1. Relational Model:

The relational model organizes data in tables (relations) with rows (tuples) and columns (attributes). Each row represents an entity and columns represent its attributes. This model is based on a strict schema, ensuring consistency in data representation. Relational databases, such as PostgreSQL and MySQL, use SQL for querying data. 

Advantages:

a. Schema enforcement, which ensures consistency.
b. Support for complex joins, which enables querying related data across multiple tables.
c. Wide adoption and mature ecosystem.

Disadvantages:

a. Limited flexibility in handling hierarchical and complex data structures.

b. Potential performance issues with joins, especially for large-scale data.


2. Document Model:

The document model stores data in semi-structured formats, such as JSON or XML. It allows for schema-less or schema-flexible storage, providing more flexibility in data representation. Document databases, such as MongoDB and Couchbase, use specialized query languages tailored to their data structures.


### Advantages:

a. Greater flexibility in handling diverse and evolving data structures.

b. Better performance for read-heavy workloads and simpler data access patterns, as related data can be stored together in a single document.

c. Easier to scale horizontally due to the denormalized nature of data storage.


### Disadvantages:

a. Lack of schema enforcement can lead to data inconsistency.

b. Limited support for complex joins, making it challenging to query related data across multiple documents.

The choice between the relational and document model depends on the specific requirements of the application. The relational model is more suitable for applications that require strict consistency, complex querying, and transactional support. In contrast, the document model is more appropriate for applications that need flexibility in data representation, faster read operations, and simpler access patterns.


## Summary
Data models are a huge subject, and in this chapter we have taken a quick look at a broad variety of different models. We didn’t have space to go into all the details of each model, but hopefully the overview has been enough to whet your appetite to find out more about the model that best fits your application’s requirements.

Historically, data started out being represented as one big tree (the hierarchical model), but that wasn’t good for representing many-to-many relationships, so the relational model was invented to solve that problem. More recently, developers found that some applications don’t fit well in the relational model either. New nonrelational “NoSQL” datastores have diverged in two main directions:

1. Document databases target use cases where data comes in self-contained docu‐ ments and relationships between one document and another are rare.
2. Graph databases go in the opposite direction, targeting use cases where anything is potentially related to everything.



## Questions during review


Declarative SQL (also known as the query language) is a way to describe what you want without specifying how to achieve it. In contrast, imperative SQL (also known as the data manipulation language) consists of statements that describe the actions you want to perform on the data.

Here's an example of declarative SQL:

```sql
-- Declarative SQL: Query to get the names of all employees who work in the 'Finance' department
SELECT employees.name
FROM employees
JOIN departments ON employees.department_id = departments.id
WHERE departments.name = 'Finance';
```


In this example, you describe what you want (names of employees in the Finance department) without specifying how to achieve it. The SQL engine is responsible for determining the best way to execute the query and retrieve the desired data.

Here's an example of imperative SQL:

```sql
-- Imperative SQL: Insert a new employee into the 'employees' table
INSERT INTO employees (name, age, department_id)
VALUES ('John Doe', 30, 1);

-- Imperative SQL: Update an employee's name in the 'employees' table
UPDATE employees
SET name = 'Jane Doe'
WHERE id = 1;

-- Imperative SQL: Delete an employee from the 'employees' table
DELETE FROM employees
WHERE id = 1;
```

In these imperative SQL examples, you are specifying the actions to perform on the data: insert a new employee, update an existing employee's name, and delete an employee. The SQL engine performs these actions directly on the data without having to determine how to achieve the desired outcome.

Note that the distinction between declarative and imperative SQL is not strict, and the terms are not always used in the same way by everyone. The examples provided here are meant to illustrate the general idea of declarative SQL as focusing on the desired outcome without specifying how to achieve it, whereas imperative SQL focuses on the actions to be performed on the data.

