# Step 06 - Working with SQL Databases

## 1. Working with Databases in NestJS

![working-with-db](./images/working-with-db.png)

A basic architectural pattern where data is processed without being persisted to any storage system.

![not database persistence](./images/not-database-persistence.png)

### Architecture Flow

- Request: A client sends a request to the system.

- UsersController: Acts as the entry point to handle the request and route it appropriately.

- UsersService: Processes the business logic but does not persist any data.

- Response: A response is sent back to the client based on the in-memory processing.

![database-persistence](./images/with-database.png)

### Architecture Flow

🔸 No Data Persistence

- Request → UsersController → UsersService (in-memory) → UsersController → Response

- No interaction with any storage or database.

🔸 With Data Persistence

- Request → UsersController → UsersService → Database

- Service reads/writes data to a persistent database, then sends it back via controller.

ORM Integration Flow: Service → ORM Layer → Database
![ORM](./images/ORM.png)

ORM (Object Relational Mapping) - a design layer that bridges the object-oriented application code with relational databases.

🔸 ORM (Object Relational Mapping)

- UsersService → ORM Layer → Database
- ORM handles object-to-table mapping, abstracts SQL queries, and provides CRUD operations through object-oriented code.

### Use Cases

- Rapid application development
- Clean abstraction of database logic
- Seamless integration with object-oriented languages

### Supported ORM in NestJS

NestJS supports integration with multiple Object-Relational Mapping (ORM) tools to facilitate database interaction in a structured and scalable way. Currently supported ORMs:

![Nest supports orms type](./images/supported-orm.png)

- **TypeORM**: A powerful and flexible ORM for SQL-based databases such as PostgreSQL, MySQL, SQLite, etc. TypeORM is deeply integrated with NestJS and follows the Data Mapper pattern with support for advanced features like migrations, relations, and query builders.
  🔗 [TypeORM Documentation](https://typeorm.io/)

- **Mongoose**: A popular ODM (Object Data Modeling) library for MongoDB, offering a straightforward schema-based solution to model application data.

🔗 [Mongoose Documentation](https://mongoosejs.com/)

### Commonly Used Databases in NestJS

NestJS offers flexible database integration using ORMs like TypeORM and Mongoose, enabling support for a wide variety of databases.

![supported db](./images/support-db.png)

✅ Popular Supported Databases:

- MySQL / MariaDB – Widely used relational database for web applications.
- PostgreSQL – Advanced relational DB with powerful features and ACID compliance.
- SQLite – Lightweight, file-based database, ideal for small projects or local development.
- Microsoft SQL Server – Enterprise-grade RDBMS used in corporate applications.
- MongoDB – Leading NoSQL document database, great with Mongoose ODM.
- Oracle – Enterprise-level relational DB for complex systems.

---

- What is an ORM?
- Installing PostgreSQL Locally
- Adding `psql` to PATH
- Connecting NestJS to PostgreSQL
- Using Async Configuration
- Theoretical Understanding of the Repository Pattern
- Creating Our First Entity - `user.entity`
- Expanding Entity Definition
- Creating First Repository
- Practice: Creating Post Entity
- Solution: Creating Post Entity
