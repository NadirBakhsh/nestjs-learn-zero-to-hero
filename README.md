# Step 13 - Guards and Decorators

## Introducing Guards

Guards in NestJS are used to approve or disapprove incoming requests. Their main purpose is to determine whether a request should be processed based on certain conditions, such as the presence and validity of an access token.


![intro guards](./images/intro-guards.png)

**Explanation:**  
A guard acts as a gatekeeper for your routes, controllers, or even the entire application. If a request does not meet the guard's criteria (for example, lacking a valid JWT access token), the guard will deny the request and throw an unauthorized exception.


**Note:**  
- Guards can be applied at the controller class level, method level, or globally.
![guards](./images/guards.png)

- Use the `@UseGuards()` decorator to apply guards.
![using guards](./images/using-guards.png)

- Guards are commonly used for authentication and authorization, such as validating JWT tokens in 
request headers.

- If the token is missing or invalid, the guard will reject the request.
![cag](./images/cag.png)


---

## Creating AccessTokenGuard
---

## Completing AccessTokenGuard Implementation
---

## Testing the AccessTokenGuard
---

## Applying AccessTokenGuard Globally
---

## Practice: Validations for JWT Environment Variables
---

## Solution: Validations for JWT Environment Variables
---

## What are Decorators?
---

## Our First Decorator
---

## Authentication Guard Strategy
---

## Create AuthenticationGuard
---

## AuthenticationGuard Implementation
---

## Understanding User Payload
---

## Create Active User Decorator
---

## Practice: Refactor `createPostDto`
---

## Solution: Refactor `createPostDto`