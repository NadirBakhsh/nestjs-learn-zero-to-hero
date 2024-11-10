# Step 02 - Understanding Modules

## 1. What are Modules?
<p>
This section outlines the fundamental concepts of using modules in NestJS, focusing on organizing and structuring applications efficiently.
</p>

#### 1. Modules as Packages of Functionality

- In NestJS, **modules** are **_containers for specific functionalities_** within the application.
- When a new functionality is introduced, it should be encapsulated within a dedicated module.
- For instance, in a blog application, a **"Users"** module might handle everything related to user functionality, from routes to data handling.

#### 2. Primary File Structure in a Module

- Each module includes a **_primary file_** which typically follows the naming pattern **_moduleName.module.ts_** (e.g., **_users.module.ts_** for a Users module).
- This primary file acts as the **entry point** for the module, coordinating other essential files within the module.

#### 3. Controller Files

- **Controllers** are essential components in a module and are generally named **_moduleName.controller.ts_** (e.g., **_users.controller.ts_**).
- They manage routing and handle incoming HTTP requests, delegating them to relevant services within the module.
- For example, requests associated with user actions, such as registration or login, would be routed through the **_users.controller.ts_** file.

#### 4. Service Files

- Modules often include **service files** (e.g., **_users.service.ts_**) that house the **business logic** for the module.
- Services provide reusable functions, allowing the controller to execute necessary operations.
- The **_users.service.ts_** file, for instance, might contain methods for user authentication, retrieving user profiles, and other user-related actions.

#### 5. Entity and Test Files

- **Entity files** (e.g., **_users.entity.ts_**) represent database models and are typically found in modules that interact with a database.
- Modules may also contain **spec files** (e.g., **_users.controller.spec.ts_**) for testing the controller and verifying routes and responses.

#### 6. Default App Module

- A default **AppModule** (**_app.module.ts_**) is created with each NestJS installation, serving as the **core module** of the application, linking to other modules.
- The **_main.ts_** file acts as the starting point of a NestJS application. When the application is launched, **_main.ts_** references the **AppModule**, which then connects to all other declared modules.



### Structure of a Basic Module in NestJS

A typical module in NestJS includes the following files:
- **Primary Module File** (e.g., **_users.module.ts_**): Entry point of the module.
- **Controller** (e.g., **_users.controller.ts_**): Manages routes and HTTP requests.
- **Service** (e.g., **_users.service.ts_**): Contains business logic.
- **Entity** (e.g., **_users.entity.ts_**): Represents database models.
- **Spec File** (e.g., **_users.controller.spec.ts_**): For testing the controller.

### Reflection

The modular approach in NestJS promotes a clean structure, making it easier to develop and manage applications by grouping related files into dedicated modules. This structure improves readability and enhances reusability, as each module operates independently while integrating smoothly with others.

---

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