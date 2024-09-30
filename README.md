# NestJS Learning Repository

Welcome to the NestJS Learning Repository! This repository contains a comprehensive guide and structured learning material for mastering NestJS, including key topics like modules, validation, dependency injection, testing, database management, and more.

## Table of Contents

1. [Introduction to NestJS](#introduction-to-nestjs)
2. [Understanding Modules](#understanding-modules)
3. [Validation and Pipes](#validation-and-pipes)
4. [Dependency Injection](#dependency-injection)
5. [Documenting Code](#documenting-code)
6. [Working with SQL Databases](#working-with-sql-databases)
7. [Database Relations](#database-relations)
8. [Environments in NestJS](#environments-in-nestjs)
9. [Exception Handling](#exception-handling)
10. [Transactions Using TypeORM](#transactions-using-typeorm)
11. [Pagination](#pagination)
12. [User Authentication](#user-authentication)
13. [Guards and Decorators](#guards-and-decorators)
14. [Refresh Tokens](#refresh-tokens)
15. [Google Authentication](#google-authentication)
16. [Serialization and Interceptors](#serialization-and-interceptors)
17. [File Uploads](#file-uploads)
18. [Notification Emails](#notification-emails)
19. [Unit Testing](#unit-testing)
20. [End to End Testing](#end-to-end-testing)
21. [MongoDB and Mongoose](#mongodb-and-mongoose)
22. [Production Deployment](#production-deployment)

---

### 1. Introduction to NestJS

- **NestJS Masterclass Introduction**
- What Will We Build in This Course
- What is NestJS
- Setting Up The Development Environment
- Installing NestJS CLI
- Creating Our First NestJS Application
- Boilerplate Code in NestJS
- Best Learning Path For The Course

### 2. Understanding Modules

- What are Modules?
- How NestJS Bootstraps (main.ts)
- Understanding the App Module
- Creating a New Users Module
- What is a REST API
- Setting Up Postman and httpYac
- Creating Controllers
- Working With Routing Decorators
- Params, Query, and Body
- Additional Request Components
- Providers in NestJS

### 3. Validation and Pipes

- What are Pipes?
- Validation and Transformation Needs
- Validating Params with Built-in Pipes
- Validating Query Params
- Introduction to DTO
- Creating Our First DTO
- Connecting DTO to Route Method
- Global Pipes and Avoiding Malicious Requests
- Converting to an Instance of DTO
- Using DTOs with Params
- Using Mapped Types to Avoid Code Duplication

### 4. Dependency Injection

- Introduction to Inversion of Control
- Dependency Injection in NestJS
- Create a Users Service
- findAll Users Method
- findOneById Users Method
- Practice: Create a Posts Module
- Solution: Create a Posts Module
- Types of Dependencies
- Create a GET Posts Route with DTO
- Return Posts From Posts Service
- Use Users Service Inside Posts Service
- Practice: Create an Auth Module
- Solution: Create an Auth Module
- Circular Dependency

### 5. Documenting Code

- Documentation with NestJS
- Open API Specification
- Enabling Swagger in NestJS
- Adding Configuration Methods to Swagger
- Documenting GET Users
- Practice: POST Endpoint and DTO for Posts Controller
- Solution: POST Endpoint and DTO for Posts Controller
- Adding Validations to CreatePostDto
- Working with Nested DTOs
- Testing Validation
- Using Swagger for Documenting CreatePostDto
- Mapped Types Using Swagger
- Getting Started with Compodoc
- Compodoc Coverage and JSDocs

### 6. Working with SQL Databases

- Working with Databases in NestJS
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

### 7. Database Relations

- Relationships in SQL Database
- Creating the Tags Entity
- Creating the Meta Options Entity
- Updating DTO Files
- Autoloading Entities
- One to One Relationships
- Uni-Directional One to One Relationship
- Creating MetaOptions Service
- Creating Post with Relationships
- Cascade Creation with Relationships
- Querying with Eager Loading
- Deleting Related Entities
- Bi-Directional One to One Relationship
- Creating a Bi-Directional Relationship
- Cascade Delete with Bi-Directional Relationship
- One to Many Relationships
- Creating One to Many Relationship
- Create Post with Author
- Eager Loading Author
- Many to Many Relationships
- Practice: Service to Create Tags
- Solution: TagsService
- Testing Tags Service
- Uni-Directional Many to Many Relationship
- Querying Many to Many Relationship
- Updating Post with New Tags
- Deleting Post and Relationship
- Bi-Directional Many to Many Relationship
- Cascade Delete with Many to Many
- Soft Delete Tags

### 8. Environments in NestJS

- Introduction to Environments
- Installing Config Module
- Using Config Service
- Confirming `NODE_ENV` While Testing
- Conditionally Loading Environments
- Inject Database Details
- Custom Configuration Files
- Config Files with Namespaces
- Module Configuration and Partial Registration
- Validating Environment Variables

### 9. Exception Handling

- Introduction to Exception Handling
- Built-in HTTP Exceptions
- Identifying Points of Failure
- Exception Handling Model Constraints
- Create User Exception Handling
- Throw a Custom Exception
- Practice: Exception Handling Post Update
- Solution: Exception Handling Post Update

### 10. Transactions Using TypeORM

- Understanding Transactions
- TypeORM QueryRunner
- Creating First Transaction
- Why Create Post is Not a Transaction
- Creating Multiple Providers
- Updating the DTO
- Practice: Handle Exceptions for CreateManyUsers
- Solution: Handle Exceptions for CreateManyUsers

### 11. Pagination

- Introduction to Pagination
- Creating Pagination Query DTO
- Adding Pagination to Query
- Pagination Module and Interface
- Using `paginateQuery`
- Building Response Object
- Complete Paginated Response

### 12. User Authentication

- Introduction to Authentication
- Hashing and Salting Passwords
- Create Hashing Providers
- Implementing Hashing Provider
- User Signup
- User SignIn Controller
- Completing the SignIn Method
- Custom Response Code
- Understanding JWTs
- Adding JWT Configuration
- Generating JWT
- JWT Token Signatures

### 13. Guards and Decorators

- Introducing Guards
- Creating AccessTokenGuard
- Completing AccessTokenGuard Implementation
- Testing the AccessTokenGuard
- Applying AccessTokenGuard Globally
- Practice: Validations for JWT Environment Variables
- Solution: Validations for JWT Environment Variables
- What are Decorators?
- Our First Decorator
- Authentication Guard Strategy
- Create AuthenticationGuard
- AuthenticationGuard Implementation
- Understanding User Payload
- Create Active User Decorator
- Practice: Refactor `createPostDto`
- Solution: Refactor `createPostDto`

### 14. Refresh Tokens

- Introduction to Refresh Tokens
- Refresh Token Configuration
- Generate Tokens Provider
- Generate Tokens Method
- Generate Refresh Token on SignIn
- Generate Access Token Using Refresh Token
- Create Refresh Token Endpoint

### 15. Google Authentication

- Google Authentication Strategy
- Create Google Project
- Setting the Configuration
- Initialize Google Auth Client
- Implementation Strategy: Google Authentication
- Implement Authentication with Google Token
- React App in Front-End
- `createGoogleUser` Method
- Complete Google Authentication
- Testing Google Authentication

### 16. Serialization and Interceptors

- Introducing Interceptors and Serialization
- Serializing User Entity
- Global Data Interceptor
- Adding API Version

### 17. File Uploads

- Introduction to File Uploads
- Setup S3 and CloudFront
- Uploads Module and Configuration
- Create Upload Entity
- Upload File Service and Controller
- `UploadToAwsProvider`
- Complete Uploads Service
- Testing File Uploads

### 18. Notification Emails

- Introduction to Notification Emails
- Setup a Mailtrap Account
- Configuration for Notification Emails
- Configure NestJS Mailer
- Creating `MailService`
- Testing Email Service
- Enabling Inline CSS

### 19. Unit Testing

- Introduction to Testing
- Test Settings
- Understanding Unit Tests
- Testing `UsersService`
- Mocking Providers
- Testing Service Method
- New Spec File for `CreateUserProvider`
- Mocking Repositories
- Mocking Other Providers
- Using Mock Repository to Test
- Running Tests

### 20. End-to-End Testing

- What is End-to-End Testing
- Test Database and Configuration
- Encapsulate App Creation Logic
- Creating First E2E Test
- Completing App Loading Lifecycle
- Encapsulate Application Bootstrap
- Introduction to Faker
- Testing Validations
- Completing All Test Cases

### 21. MongoDB and Mongoose

- Introduction to MongoDB with Mongoose
- Creating MongoDB Account
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

### 22. Production Deployment

- Deployment Strategy
- Creating an EC2 Instance
- Installing Node, NPM, and GIT
- Installing PostgreSQL
- Installing and Configuring NGINX
- Git Clone and Install
- Create the `.env` File
- Understanding Migrations
- Creating and Running Migrations
- Testing Migration on EC2
- Running with PM2

---

This repository serves as a learning tool for mastering NestJS by covering key topics comprehensively. Contributions and issues are welcome as we continue learning and building together!
