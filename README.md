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



- Installing Config Module
- Using Config Service
- Confirming `NODE_ENV` While Testing
- Conditionally Loading Environments
- Inject Database Details
- Custom Configuration Files
- Config Files with Namespaces
- Module Configuration and Partial Registration
- Validating Environment Variables