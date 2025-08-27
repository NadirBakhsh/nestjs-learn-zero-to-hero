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

**Explanation:**  
The AccessTokenGuard is a special guard in NestJS that checks if a request has a valid access token (usually a JWT) in its headers. Its main job is to extract the token from the request, validate it, and decide if the request should be allowed or denied.

**How it works:**  
1. **Extract the Request:**  
   The guard uses the NestJS `ExecutionContext` to get the incoming HTTP request object.

2. **Get the Token from Headers:**  
   It looks for the `Authorization` header in the request. The header usually looks like:  
   ```
   Authorization: Bearer <token>
   ```
   The guard splits this string and extracts the actual token part.

3. **Validate the Token:**  
   The guard uses the JWT service (from `@nestjs/jwt`) to check if the token is valid. It also uses the JWT configuration to know how to validate the token.

4. **Allow or Deny the Request:**  
   - If the token is valid, the guard returns `true` and the request is processed.
   - If the token is missing or invalid, the guard returns `false` and the request is denied (an unauthorized error is thrown).

**Key Points:**  
- The guard is created using the Nest CLI and placed in the `auth/guards/access-token` directory.
- Dependencies like the JWT service and JWT config are injected into the guard using the constructor.
- The main logic is inside the `canActivate` method, which must return `true` (allow) or `false` (deny).
- Token extraction is done in a private method that safely handles missing headers.

**Summary:**  
The AccessTokenGuard helps protect your routes by making sure only requests with a valid JWT access token can access certain endpoints. This is a common pattern for securing APIs.

[Code example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/0643c0af6c121b7c51439e263db056a437693bb1)

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