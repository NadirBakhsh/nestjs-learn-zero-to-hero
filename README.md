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

- Test Database and Configuration
- Encapsulate App Creation Logic
- Creating First E2E Test
- Completing App Loading Lifecycle
- Encapsulate Application Bootstrap
- Introduction to Faker
- Testing Validations
- Completing All Test Cases