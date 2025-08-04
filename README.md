# Step 09 - Exception Handling

### Introduction to Exception Handling

![Exception Handling](./images/introduction.png.png)

In NestJS, exception handling is a crucial part of the request and response lifecycle. NestJS provides a boundary to handle exceptions across different components such as guards, interceptors, pipes, controllers, and services. By default, exceptions are caught and translated into an HTTP response with a 500 Internal Server Error status. However, to provide meaningful error messages for easier debugging and better client communication, we should handle exceptions more elegantly.

NestJS offers several built-in HTTP exception classes, such as `BadRequestException`, `UnauthorizedException`, `NotFoundException`, among others. These can be used in our service files, where the business logic resides, to manage exceptions effectively. Controllers will primarily handle routing, while services will ensure exceptions are appropriately handled using these pre-built classes.

For more information on built-in exception filters, refer to the NestJS documentation. As we proceed through this course, we will explore using these exception classes and implementing custom exception handling strategies within our service files.


### Built-in HTTP Exceptions

The NestJS documentation provides a comprehensive guide to [built-in HTTP exceptions](https://docs.nestjs.com/exception-filters#built-in-http-exceptions).  This page is a great resource to bookmark and refer back to when you need to handle exceptions in your application.  It lists all the pre-built HTTP exceptions and how to use them.


### Identifying Points of Failure

![Identifying Points of Failure](./images/identifyung.png)

When adding exception handling to your NestJS application, it's important to identify areas in your code that are prone to errors or "corner cases." Here are common scenarios where you should consider handling exceptions:

![alt text](./images/handle-exceptions.png.png)
- **Database Operations:**  
  Any time you interact with the database (querying, inserting, updating, deleting), errors can occur due to connection issues, invalid data, or constraint violations.

- **Model Constraints:**  
  If your entity has constraints like `unique` fields (e.g., unique email for users), attempting to insert duplicate values will cause database errors. Always check for uniqueness and handle exceptions accordingly.

- **External API Calls:**  
  When your service interacts with external APIs (e.g., fetching user profiles from Google), network failures or invalid responses can occur. Exception handling ensures your app responds gracefully.

- **Middleware and Other Providers:**  
  Exception handling is not limited to services. You should also handle exceptions in middleware, guards, interceptors, and any custom providers.

**Summary:**  
Whenever your code interacts with external systems, enforces constraints, or performs operations that can fail, add exception handling using NestJS's built-in HTTP exceptions or custom strategies. This improves reliability and provides meaningful error messages to clients and developers.

---

- Exception Handling Model Constraints
- Create User Exception Handling
- Throw a Custom Exception
- Practice: Exception Handling Post Update
- Solution: Exception Handling Post Update