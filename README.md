# Step 08 - Environments in NestJS

### Introduction to Environments

#### 🚀 How NestJS Handles This

**NestJS already uses the dotenv package behind the scenes, but also provides:**
- A more powerful and structured way to handle configuration
- A built-in @nestjs/config ConfigModule for environment management

#### 💡 Why Use Different Environments?
Different environments serve different purposes:

1. Development
- Used during coding
- Uses a development DB
- Often enables automatic syncing of DB schema with entities


2. Staging
- Simulates production
- May use separate DB
- Useful for testing features before going live

3. Testing
- Uses a test DB
- Tables and data are frequently created and destroyed
- Isolated from development to prevent data loss

4. Production
- Uses live DB
- ❌ Auto-sync is turned off (for safety)
- Migrations are run manually to ensure stability

#### 🔧 The NestJS ConfigModule
- Load .env files
- Manage environment-based variables
- Provide strong typing support for environment variables
- Make configurations available via Dependency Injection

---



### Installing Config Module

In this guide, we will configure the @nestjs/config module to manage environment variables in a secure and scalable way.

![installing-config-module](./images/installing-config-module.png)

#### Why Use @nestjs/config?
- Keeps sensitive data (e.g., database URLs, API keys) outside your source code
- Enables environment-specific configurations (e.g., dev, test, prod)
- Simplifies accessing .env variables throughout your app

#### 📦 Installation
```
npm i @nestjs/config
```
#### 🛠 Setup in AppModule

```ts
// src/app.module.ts
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true, // Makes config available in all modules
    }),
    // other modules...
  ],
})
export class AppModule {}
```

#### 📁 Create .env File
Create a .env file at your project root:

```bash
# .env
S3_BUCKET=my-s3-bucket
DB_HOST=localhost
DB_PORT=5432
```

#### 📌 Summary
- Install @nestjs/config@3.2.2
- Set up .env file in root
- Use ConfigModule.forRoot({ isGlobal: true })
- Access vars via ConfigService.get()


[Source Code example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/4c5203024f96d29c5f52771cbdfbce362da91034)

---



### Using Config Service

The `ConfigService` in NestJS allows you to access environment variables and configuration settings throughout your application. By using the `ConfigService`, you can ensure that your application is configurable and environment-specific settings can be easily managed. Here's how you can use it in your NestJS application:

1. **Inject ConfigService:**

   To use the `ConfigService`, you need to inject it into your service or controller. This can be done using NestJS's dependency injection.

   ```ts
   import { Injectable } from '@nestjs/common';
   import { ConfigService } from '@nestjs/config';

   @Injectable()
   export class AppService {
     constructor(private configService: ConfigService) {}

     getDatabaseHost(): string {
       return this.configService.get<string>('DB_HOST');
     }
   }
   ```

2. **Access Environment Variables:**

   You can easily access environment variables using the `get` method provided by the `ConfigService`. The `get` method takes the name of the environment variable as a parameter and returns its value.

   ```ts
   const dbHost = this.configService.get<string>('DB_HOST');
   const dbPort = this.configService.get<number>('DB_PORT');
   ```

3. **Provide Default Values:**

   You can also provide default values in case the environment variable is not set. This is helpful in ensuring that your application runs smoothly even if some configuration is missing.

   ```ts
   const dbPort = this.configService.get<number>('DB_PORT', 5432);
   ```

4. **Type Safety:**

   The `ConfigService` provides strong typing support for environment variables, which helps in avoiding runtime errors and makes your code more predictable.

By utilizing the `ConfigService`, your application becomes more flexible and easier to maintain, as configuration settings are centralized and separated from the codebase.

[Source Code example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/dd86502e088d83df1dbd30b0802806a2210f4370)

---


### Confirming `NODE_ENV` While Testing

To handle different environment configurations in a NestJS application, you can use multiple `.env` files for different environments like development, testing, and production. Jest automatically sets the `NODE_ENV` environment variable to `test` during testing. Here’s how to configure your project to support this setup:

1. **Create Environment Files:**
   - Create separate `.env` files for each environment, e.g., `.env.development`, `.env.test`, etc.
   - Ensure that your environment-specific variables are defined in each file.

2. **Configure Jest:**
   - Modify the `jest-e2e.json` file to update the `rootDir` to point to the root of your application directory.
   - Add `modulePaths` to ensure Jest looks for modules in the correct directory.
   ```json
   {
     "rootDir": "../",
     "modulePaths": ["<rootDir>"]
   }
   ```

3. **Update `package.json`:**
   - In the Jest configuration inside `package.json`, set the `rootDir` to the application root and add `modulePaths`.
   ```json
   {
     "jest": {
       "rootDir": ".",
       "modulePaths": ["<rootDir>"]
     }
   }
   ```

4. **Modify Test File:**
   - In your `app.e2e-spec.ts` file, access the `NODE_ENV` using `process.env.NODE_ENV` and log it to verify its value.
   ```ts
   console.log(process.env.NODE_ENV);
   ```

5. **Run End-to-End Tests:**
   - Use the script in `package.json` to run your end-to-end tests:
   ```bash
   npm run test:e2e

[Source Code example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/19c71c113951bd554615858a5b11917e0e1f5d45)

---


### Conditionally Loading Environments

![cle](./images/cle.png)

### Conditionally Loading Environments

To conditionally load different environment variables based on different environments you can pass an expression to the `envFilePath` property of the `ConfigModule`.

For example, if you want to load a different environment file depending on the `NODE_ENV` variable, you can do the following:

[Source Code example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/19c71c113951bd554615858a5b11917e0e1f5d45)

---



- Inject Database Details
- Custom Configuration Files
- Config Files with Namespaces
- Module Configuration and Partial Registration
- Validating Environment Variables