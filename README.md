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

## Testing `UsersService`

## Mocking Providers

## Testing Service Method

## New Spec File for `CreateUserProvider`

## Mocking Repositories

## Mocking Other Providers

## Using Mock Repository to Test

## Running Tests