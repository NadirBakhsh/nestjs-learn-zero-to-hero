# Step 03 - Validation and Pipes

## 1. What are Pipes?
![Nextjs what is pipes](./images/what-is-pipes.png)

Pipes are a powerful feature in NestJS used to transform and validate incoming request data before it reaches the controller. They act as a layer in the request-response lifecycle to ensure that data sent to your application is in the correct format and adheres to your expected validation

 **Overview**
![Nextjs Req Res lifecycle](./images/req-res-lifecycle.png)

NestJS is a progressive Node.js framework for building efficient, reliable, and scalable server-side applications. This project explores the various components involved in the request-response lifecycle:

- Middleware
- Filters
- Guards
- Interceptors
- Pipes
- Controllers
- Providers/Services

![Nextjs Pipes](./images/pipes.png)
### **Pipes**
- **Purpose**: Validate and transform incoming request data.
- **Usage**: 
  - **Validation**: Ensure data matches schema.
  - **Transformation**: Convert data to the required format.


![Nextjs Pipes](./images/why-use-pipes.png)
### **Reasons to Use Pipes**

**Centralized Validation Logic**
Pipes allow you to consolidate your validation logic in a reusable way, rather than repeating it in multiple places.
By leveraging tools like class-validator with ValidationPipe, you can declaratively define validation rules in Data Transfer Objects (DTOs).

![Nextjs Pipes](./images/type-of-pipes.png) 
### **Built-in Pipes:**

NestJS provides several built-in pipes for common validation and transformation needs.

Examples:
- ValidationPipe – Validates data using class-validator decorators.
- ParseIntPipe – Transforms a string into an integer.
- ParseBoolPipe – Transforms a string into a boolean ("true" to true).
- ParseArrayPipe – Ensures incoming data is an array.
- ParseUUIDPipe – Validates and parses UUID strings.

### **Custom Pipes:**
- Developers can create custom pipes for specific use cases.
- Useful for advanced validation logic or transformations tailored to the application's requirements.

---

## 2. Validation and Transformation Needs

## 3. Validating Params with Built## 2.in Pipes
## 4. Validating Query Params
## 5. Introduction to DTO
## 6. Creating Our First DTO
## 7. Connecting DTO to Route Method
## 8. Global Pipes and Avoiding Malicious Requests
## 9. Converting to an Instance of DTO
## 10. Using DTOs with Params
## 11. Using Mapped Types to Avoid Code Duplication