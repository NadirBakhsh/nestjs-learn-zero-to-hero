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



- Using Config Service
- Confirming `NODE_ENV` While Testing
- Conditionally Loading Environments
- Inject Database Details
- Custom Configuration Files
- Config Files with Namespaces
- Module Configuration and Partial Registration
- Validating Environment Variables