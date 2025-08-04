# Step 09 - Exception Handling

### Introduction to Exception Handling

![Exception Handling](./images/introduction.png.png)

In NestJS, exception handling is a crucial part of the request and response lifecycle. NestJS provides a boundary to handle exceptions across different components such as guards, interceptors, pipes, controllers, and services. By default, exceptions are caught and translated into an HTTP response with a 500 Internal Server Error status. However, to provide meaningful error messages for easier debugging and better client communication, we should handle exceptions more elegantly.

NestJS offers several built-in HTTP exception classes, such as `BadRequestException`, `UnauthorizedException`, `NotFoundException`, among others. These can be used in our service files, where the business logic resides, to manage exceptions effectively. Controllers will primarily handle routing, while services will ensure exceptions are appropriately handled using these pre-built classes.

For more information on built-in exception filters, refer to the NestJS documentation. As we proceed through this course, we will explore using these exception classes and implementing custom exception handling strategies within our service files.


- Built-in HTTP Exceptions
- Identifying Points of Failure
- Exception Handling Model Constraints
- Create User Exception Handling
- Throw a Custom Exception
- Practice: Exception Handling Post Update
- Solution: Exception Handling Post Update