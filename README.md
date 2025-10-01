# Step 19 - Unit Testing

## Introduction to Testing

Unit testing is a crucial aspect of building robust and maintainable NestJS applications. In this section, we'll explore how to write effective unit tests for your NestJS components.

**Understanding Test File Structure**
In NestJS, unit tests are written in `.spec.ts` files that are located alongside the components they test. For example:
- `users.service.ts` has a corresponding `users.service.spec.ts`
- `create-user.provider.ts` has a corresponding `create-user.provider.spec.ts`

This file organization follows NestJS CLI conventions and keeps tests close to the code they validate.

**Jest Integration**
NestJS comes with built-in Jest integration, providing:
- Pre-configured testing scripts in `package.json`
- No additional setup required for basic testing
- Seamless integration with NestJS modules and dependency injection

**What Are Unit Tests?**
Unit tests focus on testing individual methods or functions in isolation. When testing a method like `createUser()`, the unit test should only verify that specific function's behavior, not the entire application flow.

**The Challenge: Dependencies**
Most methods in a NestJS application have dependencies:
- Services inject repositories, other services, or external modules
- Methods rely on database connections, external APIs, or other components
- Testing in isolation requires handling these dependencies

**The Solution: Mocking**
Jest provides powerful mocking capabilities that allow you to:
- Replace real dependencies with controlled mock implementations
- Test your code without relying on external systems
- Verify that your methods interact correctly with their dependencies
- Control the behavior of dependencies to test different scenarios

**Benefits of Unit Testing**
- **Isolation**: Test individual components without external dependencies
- **Speed**: Fast execution without database or network calls
- **Reliability**: Consistent results regardless of external system states
- **Documentation**: Tests serve as living documentation of expected behavior

**What You'll Learn**
This section will cover:
- Setting up and configuring tests
- Writing unit tests for services and providers
- Creating and using mocks effectively
- Testing different scenarios and edge cases
- Best practices for maintainable tests

Whether you're new to testing or have previous experience, this section will transform how you approach application development. Once you experience the confidence that comes with comprehensive testing, you'll never want to write code without tests again.

---

## Test Settings

Before writing and running tests, ensure you have the correct Jest configuration in place. Proper configuration is essential for Jest to locate and execute your test files correctly.

**1. Configure package.json**
In your `package.json` file, add or verify the Jest configuration:

```json
{
  "jest": {
    "rootDir": ".",
    "modulePaths": ["<rootDir>"]
  }
}
```

**Key points:**
- `rootDir`: Set to "." since package.json is in the project root
- `modulePaths`: Uses the root directory as a placeholder for module resolution
- This configuration helps Jest find all `.spec.ts` files throughout your project

**2. Configure jest-e2e.json**
In the `test/jest-e2e.json` file, ensure the following configuration:

```json
{
  "rootDir": "..",
  "modulePaths": ["<rootDir>"]
}
```

**Key points:**
- `rootDir`: Set to ".." because this file is inside the `test` directory, one level below the project root
- `modulePaths`: Uses the same pattern but relative to the test directory location

**Why This Configuration Matters:**
- **Module Resolution**: Jest needs to know where to find your modules and dependencies
- **Test Discovery**: Proper root directory helps Jest locate all test files
- **Import Paths**: Ensures that imports in your test files resolve correctly
- **Error Prevention**: Incorrect configuration leads to module resolution errors during test execution

**Verification:**
After setting up these configurations, Jest will be able to:
- Find and execute all `.spec.ts` files in your project
- Resolve module imports correctly
- Run both unit tests and end-to-end tests without path-related errors

Make sure both configurations are in place before proceeding with writing tests to avoid common Jest setup issues.

---

## Understanding Unit Tests

![understanding-unit-tests](./images/understanding-unit-tests.png)

Let's examine the default test file created by NestJS to understand the structure and components of unit tests.

**Test File Structure**
All unit test files have a `.spec.ts` extension and follow a consistent pattern. The default `app.controller.spec.ts` provides a great example:

**1. Describe Blocks**
```typescript
describe('AppController', () => {
  // Test content goes here
});
```
- `describe()` defines the module or component being tested
- Can contain nested describe blocks for better organization
- Helps organize tests by functionality (e.g., POST endpoints, PATCH endpoints)

**2. Lifecycle Methods**
Four lifecycle methods are available within describe blocks:

- **`beforeEach()`**: Runs before each individual test
- **`beforeAll()`**: Runs once before all tests in the describe block
- **`afterEach()`**: Runs after each individual test  
- **`afterAll()`**: Runs once after all tests complete

**3. Module Setup (beforeEach)**
```typescript
beforeEach(async () => {
  const app: TestingModule = await Test.createTestingModule({
    controllers: [AppController],
    providers: [AppService],
  }).compile();

  appController = app.get<AppController>(AppController);
});
```

This pattern:
- Creates a testing module using `Test.createTestingModule()`
- Includes necessary controllers and providers (dependencies)
- Compiles the module for testing
- Extracts the controller instance for use in tests

**4. Individual Tests (it blocks)**
```typescript
it('controller should be defined', () => {
  expect(appController).toBeDefined();
});
```

Each test:
- Uses `it()` to define what is being tested
- Contains assertions using `expect()` statements
- Should test one specific behavior or outcome

**5. Assertions and Expectations**
Jest provides many assertion methods:
- `toBeDefined()`: Checks if value is not undefined
- `toBeNull()`: Checks if value is null
- `toBe()`: Checks for exact equality
- `toEqual()`: Checks for deep equality
- Many more available for different scenarios

**Running Tests**
Use the following npm scripts:
- `npm run test`: Run all tests once
- `npm run test:watch`: Run tests in watch mode (reruns on file changes)
- `npm run test -- --testNamePattern="AppController"`: Run specific test files

**Watch Mode with File Patterns**
```bash
npm run test:watch -- --testNamePattern="app.controller"
```
This runs only tests matching the specified pattern, useful during development to focus on specific components.

**Test Results**
Jest provides detailed feedback:
- ✅ **Passing tests**: Shows green checkmarks with test descriptions
- ❌ **Failing tests**: Shows detailed error messages with:
  - Breadcrumb path to the failing test
  - Exact line of code that failed
  - Expected vs actual values
  - Clear error descriptions

This structure and pattern will be consistent across all test files we create, making it easy to write and maintain comprehensive test suites.

## Testing `UsersService`

![testing-users-service](./images/testing-users-service.png)

Now let's create a test file for a more complex service that has dependencies. We'll test the `UsersService` to understand how to handle dependency injection in unit tests.

**1. Create the Test File**
Create a new file `users.service.spec.ts` in the `src/users/providers/` directory alongside the `users.service.ts` file.

**2. Basic Test Structure**
Start with the boilerplate test structure:

```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { UsersService } from './users.service';

describe('UsersService', () => {
  let service: UsersService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [UsersService],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });
});
```

**3. The Dependency Problem**
Running this test will fail with an error like:
```
Nest can't resolve dependencies of the UsersService
```

This happens because `UsersService` has constructor dependencies:
- `UsersRepository` (TypeORM repository)
- `CreateUserProvider`
- Other injected providers

**4. Understanding the Issue**
When you examine `users.service.ts`, you'll see the constructor:

```typescript
constructor(
  @InjectRepository(User) private usersRepository: Repository<User>,
  private readonly createUserProvider: CreateUserProvider,
  // ... other dependencies
) {}
```

The testing module can't instantiate `UsersService` without these dependencies being available.

**5. Running the Test**
Try running the test to see the dependency error:

```bash
npm run test:watch -- --testNamePattern="users.service"
```

The test will fail because Jest can't resolve the required dependencies.

**Key Learning Points:**
- **Dependency Injection in Tests**: Services with dependencies need those dependencies to be available during testing
- **Isolation Challenge**: Unit tests should test components in isolation, but dependencies create coupling
- **Real Dependencies Problems**: Using real repositories would require database connections and actual data
- **Next Step**: We need to use mocking to replace real dependencies with controlled test doubles

This dependency resolution error demonstrates why mocking is essential for unit testing in NestJS applications. In the next section, we'll learn how to mock these dependencies.

[See the commit for changes](https://github.com/NadirBakhsh/nestjs-resources-code/commit/172cc490e5db88783f69812aedb0b4128008ded7)

## Mocking Providers

To test services with dependencies, you need to mock those dependencies so your unit test can focus on the service logic itself.

**1. Mocking Custom Providers**
For each provider required by `UsersService`, use the following pattern in your test file:

```typescript
{
  provide: CreateGoogleUserProvider,
  useValue: {},
},
{
  provide: FindOneByGoogleIdProvider,
  useValue: {},
},
{
  provide: FindOneUserByEmailProvider,
  useValue: {},
},
{
  provide: CreateUserProvider,
  useValue: {},
},
{
  provide: CreateManyUserProvider,
  useValue: {},
},
```
- `provide`: The class or token to inject
- `useValue`: The mock value (empty object for basic tests)

**2. Mocking TypeORM Repository and DataSource**
Repositories and data sources require special handling:

```typescript
import { getRepositoryToken } from '@nestjs/typeorm';
import { DataSource } from 'typeorm';
import { User } from '../entities/user.entity';

{
  provide: DataSource,
  useValue: {},
},
{
  provide: getRepositoryToken(User),
  useValue: {},
},
```
- Use `getRepositoryToken(User)` to mock the User repository

**3. Complete Providers Array Example**
Your providers array in `users.service.spec.ts` should look like:

```typescript
providers: [
  UsersService,
  { provide: CreateGoogleUserProvider, useValue: {} },
  { provide: FindOneByGoogleIdProvider, useValue: {} },
  { provide: FindOneUserByEmailProvider, useValue: {} },
  { provide: CreateUserProvider, useValue: {} },
  { provide: CreateManyUserProvider, useValue: {} },
  { provide: DataSource, useValue: {} },
  { provide: getRepositoryToken(User), useValue: {} },
],
```

**4. Why Use Mocks?**
- You are not testing the dependencies themselves—those should have their own unit tests.
- Mocks allow you to instantiate the service and test its methods in isolation.
- No real database or external service is involved.

**5. Running the Test**
With all dependencies mocked, your test should now pass:

```bash
npm run test:watch -- --testNamePattern="users.service"
```

**6. Assertion Example**
```typescript
it('should be defined', () => {
  expect(service).toBeDefined();
});
```

**Summary**
- Mock all dependencies using `provide` and `useValue`
- Use `getRepositoryToken` for repositories
- Use empty objects for basic mocks
- Focus your tests on the service logic, not its dependencies

Practice writing these mocks to get comfortable with unit testing in NestJS.

[See the commit for changes](https://github.com/NadirBakhsh/nestjs-resources-code/commit/a7683886dfc45ff69757e95350358dd92fc5a06e)

## Testing Service Method

![Testing Service Method](./images/testing-service-method.png)


```typescript
import { CreateUserDto } from '../dto/create-user.dto';

// Mock implementation for CreateUserProvider
const mockCreateUserProvider = {
  createUser: (dto: CreateUserDto) =>
    Promise.resolve({
      id: 1,
      firstName: dto.firstName,
      lastName: dto.lastName,
      email: dto.email,
      password: dto.password,
    }),
};

describe('createUser', () => {
  it('should be defined', () => {
    expect(service.createUser).toBeDefined();
  });

  it('should call createUser on CreateUserProvider', async () => {
    const user = await service.createUser({
      firstName: 'John',
      lastName: 'Doe',
      email: 'john@example.com',
      password: 'securepassword',
    });
    expect(user.firstName).toEqual('John');
  });
});
```
[See the commit for changes](https://github.com/NadirBakhsh/nestjs-resources-code/commit/c32cdd12fa4f4b1b5fb669e66cb682f2397a98e3)

## New Spec File for `CreateUserProvider`

To unit test the `CreateUserProvider`, create a new spec file and mock its dependencies.

**1. Create the Spec File**
- In the same directory as `create-user.provider.ts`, create `create-user.provider.spec.ts`.

**2. Boilerplate Test Setup**
- Copy the basic test structure from your previous service spec files.
- Remove unnecessary imports and logic, keeping only the essentials.

**3. Mock Dependencies**
- Identify dependencies from the constructor: `MailService`, `HashingProvider`, and the user repository.
- Add each dependency to the providers array using `{ provide, useValue: {} }`.
- For the repository, use `getRepositoryToken(User)` and also mock `DataSource`.

Example providers array:
```typescript
providers: [
  CreateUserProvider,
  { provide: MailService, useValue: {} },
  { provide: HashingProvider, useValue: {} },
  { provide: DataSource, useValue: {} },
  { provide: getRepositoryToken(User), useValue: {} },
],
```

**4. Extract the Provider Instance**
- Declare a variable in the describe scope:
  ```typescript
  let provider: CreateUserProvider;
  ```
- In `beforeEach`, assign the instance:
  ```typescript
  provider = module.get<CreateUserProvider>(CreateUserProvider);
  ```

**5. Basic Assertion**
- Add a simple test to verify the provider is defined:
  ```typescript
  it('should be defined', () => {
    expect(provider).toBeDefined();
  });
  ```

**6. Run the Test**
- Use watch mode to run the test:
  ```bash
  npm run test:watch -- --testNamePattern="create-user.provider"
  ```
- The test should pass, confirming dependencies are injected correctly.

With this setup, you have a working spec file for `CreateUserProvider` and can proceed to write more detailed tests using Jest's mocking features.

[commit changes](https://github.com/NadirBakhsh/nestjs-resources-code/commit/ba4e0ce0546dee688dcca0ad244fdc3027270236)

## Mocking Repositories
![Mocking Repositories](./images/mocking-repositories.png)

  
  ## Mocking a Repository with Jest in TypeORM-based Applications

  This section explains how to mock a TypeORM repository using Jest's native mocking methods, enabling effective and type-safe unit testing of providers or services that depend on repository methods.

  ### 1. Creating a Mock Repository Type

  We define a generic `MockRepository<T>` type that can be applied to any TypeORM repository. This type uses TypeScript's utility types to create a partial mapping of all repository methods to Jest mock functions. This allows us to mock only the methods we need for our tests, rather than the entire repository interface.

  - **Generic Type**: The type is generic (`<T>`) so it can be used with any entity repository (e.g., `UserRepository`, `PostRepository`).
  - **Partial Mapping**: By using `Partial`, we ensure that only the required methods need to be mocked.
  - **Record Type**: We use `Record<keyof Repository<T>, jest.Mock>` to map each method/property of the repository to a Jest mock function.

  ### 2. Creating a Mock Repository Factory Function

  A factory function, `createMockRepository`, is implemented to generate a mock repository object. This function returns an object with only the necessary methods mocked using `jest.fn()`. For example, if the provider under test uses `findOne`, `create`, and `save`, only these methods are mocked.

  - **Type Safety**: The returned object is typed as `MockRepository<T>`, ensuring type safety in tests.
  - **Customizable**: You can easily extend the factory to mock additional methods as needed.

  ### 3. Using the Mock Repository in Tests

  - **Assignment**: The mock repository is assigned to a variable (e.g., `usersRepository`) and provided to the testing module.
  - **Dependency Injection**: When the provider (e.g., `CreateUserProvider`) is instantiated in the test, it receives the mock repository instead of the real one.
  - **Jest Integration**: The mocked methods (`findOne`, `create`, `save`) can be configured in each test to return specific values or behaviors using Jest's API.

  ### 4. Extracting the Mock Repository from the Testing Module

  To perform assertions or further configure the mock repository, it is extracted from the testing module using `module.get(getRepositoryToken(User))`. This ensures that the mock repository used in the provider is accessible in the test scope.

  ### 5. Extending to Other Providers

  The same approach can be used to mock other dependencies, such as mail services or hashing providers, by creating similar mock factory functions and types.

  ---

  **Summary:**  
  By defining a generic mock repository type and a factory function, we can efficiently and type-safely mock only the required repository methods for our tests. This approach leverages Jest's mocking capabilities and TypeScript's type system, resulting in maintainable and robust unit tests for providers and services that depend on TypeORM repositories.

[See the commit for changes](https://github.com/NadirBakhsh/nestjs-resources-code/commit/81aed682fffc7f52be34a6b026864f7e426116c8)



## Mocking Other Providers

## Using Mock Repository to Test

## Running Tests