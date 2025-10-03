# Step 20 - End to End Testing

## What is End-to-End Testing

![End-to-End Testing](./images/end-to-end-testing.png)

End-to-End (E2E) testing represents a more holistic approach to testing compared to unit testing. While unit tests focus on testing individual methods in isolation, E2E tests validate complete user workflows and API endpoints from start to finish.

**Understanding E2E vs Unit Testing**
- **Unit Tests**: Test individual methods and functions in isolation with mocked dependencies
- **E2E Tests**: Test complete API endpoints with real database interactions and full application stack

**What E2E Testing Covers**
E2E tests validate:
- **API Endpoints**: Verify that endpoints return correct data and status codes
- **Request/Response Flow**: Test the complete HTTP request-response cycle
- **Validation Logic**: Ensure input validation works correctly
- **Database Integration**: Test actual database operations (create, read, update, delete)
- **Authentication & Authorization**: Verify security measures work end-to-end
- **Business Logic**: Test complete user workflows and business processes

**File Naming Convention**
E2E test files follow a specific naming pattern:
```
users.post.e2e-spec.ts    // Tests POST endpoints for users
users.get.e2e-spec.ts     // Tests GET endpoints for users  
users.patch.e2e-spec.ts   // Tests PATCH endpoints for users
```

**Key Differences from Unit Tests:**
- Unit tests use `.spec.ts` extension
- E2E tests use `.e2e-spec.ts` extension
- This helps Jest identify and categorize different types of tests

**Directory Structure**
E2E tests are organized in the `test/` directory:
```
project-root/
├── src/                     # Application source code
├── test/                    # E2E tests directory
│   ├── users/              # Module-specific tests
│   │   ├── users.post.e2e-spec.ts
│   │   ├── users.get.e2e-spec.ts
│   │   └── users.patch.e2e-spec.ts
│   └── jest-e2e.json      # E2E Jest configuration
```

**Database Considerations**
For E2E testing, you need a test database:
- **Recommended**: Use the same database type as production (PostgreSQL in our case)
- **Why Not SQLite**: Production-specific features (PostgreSQL functions, data types) won't be available
- **Best Practice**: Create a separate test database to avoid affecting development data

**Required Libraries**
E2E testing uses a combination of tools:
- **Jest**: Test runner and assertion library
- **Supertest**: HTTP request library for testing API endpoints
- **Faker.js**: Generates realistic fake data for testing

**How Supertest Works**
```typescript
import * as request from 'supertest';

// Example E2E test
it('should create a new user', () => {
  return request(app.getHttpServer())
    .post('/users')
    .send({
      firstName: 'John',
      lastName: 'Doe',
      email: 'john@example.com'
    })
    .expect(201)
    .expect((res) => {
      expect(res.body.email).toBe('john@example.com');
    });
});
```

**Test Data Generation with Faker**
```typescript
import { faker } from '@faker-js/faker';

const testUser = {
  firstName: faker.person.firstName(),
  lastName: faker.person.lastName(),
  email: faker.internet.email(),
  password: faker.internet.password()
};
```

**Benefits of E2E Testing**
- **Real-World Validation**: Tests how users actually interact with your API
- **Integration Verification**: Ensures all components work together correctly
- **Confidence**: Provides high confidence that features work end-to-end
- **Regression Prevention**: Catches issues that unit tests might miss
- **Documentation**: Serves as living documentation of API behavior

**When to Use E2E Tests**
- Testing critical user workflows
- Validating API contracts
- Ensuring database operations work correctly
- Testing authentication flows
- Verifying complex business logic that spans multiple layers

**E2E Test Strategy**
- Focus on happy path scenarios and critical edge cases
- Test one endpoint thoroughly rather than many endpoints superficially
- Use realistic test data that represents actual usage patterns
- Clean up test data after each test to ensure isolation

This section will guide you through setting up and writing comprehensive E2E tests that validate your NestJS application's behavior from the user's perspective, ensuring your APIs work correctly in real-world scenarios.
---

## Test Database and Configuration

![Test Database Configuration](./images/test-db-configuration.png)

Before writing any end-to-end tests, you need to set up a dedicated test database and environment configuration. This ensures your tests run in isolation and do not affect your development data.

### 1. Create a Test Database

1. **Open pgAdmin** and navigate to the **Databases** section.
2. **Right-click** on `Databases` and select **Create > Database...**
3. **Enter a name** for your test database, e.g., `nestjs_blog_test`.
4. **Set the owner** (e.g., `postgres`).
5. **Click Save** to create the database.

Your test database is now ready for use.

### 2. Duplicate Environment Configuration

1. In your project, **locate** the `.env.development` file.
2. **Duplicate** this file and rename the copy to `.env.test`.
3. **Edit** the `DATABASE_NAME` (or equivalent variable) in `.env.test` to match your new test database name (e.g., `nestjs_blog_test`).

Example:
```env
# .env.test
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_USER=postgres
DATABASE_PASSWORD=yourpassword
DATABASE_NAME=nestjs_blog_test
```

### 3. How It Works

- When running tests, set `NODE_ENV=test`. Your application will automatically load `.env.test`.
- All test operations will use the test database, keeping your development data safe.

You are now ready to configure and run end-to-end tests using this isolated environment.

---


## Encapsulate App Creation Logic

To avoid code duplication between production and testing environments, we need to extract the application configuration logic into a reusable function. This ensures both production and test applications use identical middleware configurations.

**The Problem**
In your `main.ts` file, you likely have middleware configuration like this:

```typescript
// main.ts - Before refactoring
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Multiple middleware configurations
  app.useGlobalPipes(new ValidationPipe());
  
  // Swagger configuration
  const config = new DocumentBuilder()
    .setTitle('NestJS Blog API')
    .setDescription('Use the base API URL as http://localhost:3000')
    .setTermsOfService('http://localhost:3000/terms-of-service')
    .setLicense('MIT License', 'https://github.com/git/git-scm.com/blob/main/MIT-LICENSE.txt')
    .addServer('http://localhost:3000')
    .setVersion('1.0')
    .build();
  
  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api', app, document);
  
  // AWS SDK Configuration
  AWS.config.update({
    credentials: {
      accessKeyId: configService.get('appConfig.awsAccessKeyId'),
      secretAccessKey: configService.get('appConfig.awsSecretAccessKey'),
    },
    region: configService.get('appConfig.awsRegion'),
  });
  
  // Enable CORS
  app.enableCors();
  
  await app.listen(3000);
}
```

**The Solution: Create a Reusable Function**

1. **Create `src/app.create.ts`**:

```typescript
import { INestApplication, ValidationPipe } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
import * as AWS from 'aws-sdk';
import { DataResponseInterceptor } from './common/interceptors/data-response/data-response.interceptor';

export function appCreate(app: INestApplication): void {
  const configService = app.get(ConfigService);

  // Global Validation Pipe
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
      transformOptions: {
        enableImplicitConversion: true,
      },
    }),
  );

  // Global Data Response Interceptor
  app.useGlobalInterceptors(new DataResponseInterceptor());

  // Swagger Configuration
  const config = new DocumentBuilder()
    .setTitle('NestJS Blog API')
    .setDescription('Use the base API URL as http://localhost:3000')
    .setTermsOfService('http://localhost:3000/terms-of-service')
    .setLicense(
      'MIT License',
      'https://github.com/git/git-scm.com/blob/main/MIT-LICENSE.txt',
    )
    .addServer('http://localhost:3000')
    .setVersion('1.0')
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api', app, document);

  // AWS SDK Configuration
  AWS.config.update({
    credentials: {
      accessKeyId: configService.get('appConfig.awsAccessKeyId'),
      secretAccessKey: configService.get('appConfig.awsSecretAccessKey'),
    },
    region: configService.get('appConfig.awsRegion'),
  });

  // Enable CORS
  app.enableCors();
}
```

2. **Refactor `main.ts`**:

```typescript
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { appCreate } from './app.create';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Add all middleware using the reusable function
  appCreate(app);
  
  await app.listen(3000);
}
bootstrap();
```

**Benefits of This Approach**

**Code Reusability**: 
- Same configuration logic used in both production and testing
- Eliminates duplicate middleware setup code

**Consistency**:
- Ensures test environment mirrors production exactly
- Reduces configuration drift between environments

**Maintainability**:
- Single place to update middleware configuration
- Easier to add new middleware across all environments

**Reduced Errors**:
- Less chance of forgetting middleware in test setup
- Consistent behavior across environments

**How to Use in Tests**
In your E2E tests, you can now use the same function:

```typescript
// In your E2E test setup
import { Test } from '@nestjs/testing';
import { AppModule } from '../src/app.module';
import { appCreate } from '../src/app.create';

let app: INestApplication;

beforeAll(async () => {
  const moduleFixture = await Test.createTestingModule({
    imports: [AppModule],
  }).compile();

  app = moduleFixture.createNestApplication();
  
  // Apply the same middleware as production
  appCreate(app);
  
  await app.init();
});
```

**Key Points**
- **Function Type**: `appCreate(app: INestApplication): void`
- **Parameter**: Receives the NestJS application instance
- **Purpose**: Configures all middleware consistently
- **Return**: Void - modifies the app instance in place

**What Gets Configured**
- Global validation pipes with transformation options
- Global response interceptors
- Swagger documentation setup
- AWS SDK configuration
- CORS enabling
- Any other global middleware

This pattern ensures your E2E tests run against an application configured identically to production, providing confidence that your tests reflect real-world behavior.

---

- Creating First E2E Test
- Completing App Loading Lifecycle
- Encapsulate Application Bootstrap
- Introduction to Faker
- Testing Validations
- Completing All Test Cases