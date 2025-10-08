# Step 21 - MongoDB and Mongoose

## Introduction to MongoDB with Mongoose

![MongoDB Structure](./images/intro%20mongodb%20+%20mongoose.png)

MongoDB is a NoSQL database that stores data as JSON-like documents, making it ideal for handling unstructured data. In NestJS, Mongoose is used as an abstraction layer to interact with MongoDB, similar to how TypeORM works with SQL databases. Instead of entities, you define schemas to describe the structure of collections. Models are injected into services to perform CRUD operations. This approach allows you to work with MongoDB efficiently within your NestJS applications.


![MongoDB Structure](./images/about-mogodb.png)

![SQL vs NoSQL](./images/sql%20vs%20nosql.png)

### **SQL vs NoSQL refers to two broad categories of database systems:**

SQL (Structured Query Language) Databases
Type: Relational databases (e.g., MySQL, PostgreSQL, SQL Server)
Data Structure: Tables with rows and columns; each row is a record, each column is a field.
Schema: Fixed and predefined; you must define the structure before inserting data.
Relationships: Supports JOINs to relate data across tables.
Transactions: Strong ACID compliance (Atomicity, Consistency, Isolation, Durability).
Use Case: Ideal for structured data and complex queries (e.g., banking, inventory).
NoSQL (Not Only SQL) Databases
Type: Non-relational databases (e.g., MongoDB, Cassandra, Redis)
Data Structure: Flexible; can be document-based, key-value, graph, or column-family.
Schema: Dynamic; you can store different structures in the same collection.
Relationships: Limited JOIN support; often handled at the application level.
Transactions: May offer eventual consistency; ACID support varies.
Use Case: Great for unstructured or semi-structured data, scalability, and rapid development (e.g., social media, IoT).


## Creating MongoDB Account

To start working with MongoDB, create a free account at mongodb.com and set up a cloud cluster using the M0 free tier. This approach avoids local installation and provides access to features available only in clusters. After registration, whitelist your IP address for access, create a database user, and copy your connection string. You can use MongoDB Compass (GUI tool) to connect and browse your cluster, similar to how PgAdmin works for PostgreSQL. Create your database and collections (e.g., users) in Compass for development.

- MongoDB Configuration
- First Schema - User
- Post Schema
- Create Using Model
- Mongoose Sub Documents
- Single Sub Document
- Practice: Tags Module
- Solution: Tags Module
- Practice: Tags Service + Controller
- Solution: Tags Service + Controller
- Array of Sub Documents
- Querying Sub Documents