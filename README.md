# Step 12 - User Authentication

## Introduction to Authentication

![Authentication](./images/auth-intro.png)

### Authentication in NestJS – Overview

![Type of Authentication](./images/type-auth.png)


When building a **NestJS** application, there are multiple ways to authenticate a user.  
In this course, we will focus on **two specific methods**:

1. **Password-Based Authentication** (covered in this section)  
2. **Third-Party Authentication** (e.g., Google Authentication – covered in another section)



### 1. What is Password-Based Authentication?
In **password-based authentication**, a user provides their **email** and **password** during sign-up.  
When they log in, the system validates these credentials.

The authentication process involves:
- **Sign-Up** → Accept email & password → Securely store it in the database.
- **Login** → Compare provided credentials with stored data → Authenticate if matched.

![Authentication User](./images/authenticationg-user.png)


### 2. Why Not Store Passwords in Plain Text?
**Storing passwords as plain text is dangerous** because:
- Developers with DB access can see all user passwords.
- If a database breach occurs, hackers can access passwords without effort.
- Users often reuse the same password across services → a breach here can compromise their accounts elsewhere.

**Solution:** Always **hash** and **salt** passwords before storing them.


![Authentication User Password Hash](./images/hashing-passward.png)


### 3. Hashing and Salting
- **Hashing** → Converts the password into a fixed-length scrambled string (irreversible).
- **Salting** → Adds random data to the password before hashing to prevent common attacks.

✅ Benefit → Even if two users have the same password, their stored hash will be different.


### 4. Authentication Flow with JWT
Once the password is hashed & stored:
1. **User Login:**
   - User provides email & password.
   - The server hashes the entered password and compares it to the stored hash.
2. **If Match → Generate JWT Token:**
   - **JWT** = **JSON Web Token** → a signed token containing user identity information.
   - Sent back to the user upon successful authentication.


### 5. Using JWT for Subsequent Requests
- For endpoints requiring authentication, the client sends the **JWT** in the request header.
- The server verifies:
  - The JWT signature is valid.
  - The token has not been tampered with.
  - The token has not expired.
- If valid → request is authorized.

![Authentication & authorization](./images/authorization.png)



### 6. Key Components in This Section
In this section of the course, we will cover:
- **Hashing & Salting Passwords**
- **Storing Secure Passwords in the Database**
- **Comparing Hashed Passwords During Login**
- **Issuing JWT Tokens**
- **Verifying JWTs for Protected Endpoints**
- **Authorizing Requests Based on JWT**

#### Summary Flow Diagram
```
Sign-Up:
   User → Email + Password → Hash + Salt → Store in DB

Login:
   User → Email + Password → Hash + Compare with DB → If Match → Issue JWT

Subsequent Requests:
   Client → Sends JWT → Server Verifies Signature → Authorize or Reject
```

By the end of this section, you will be able to implement **secure, password-based authentication** in your **NestJS** applications.


---

## Hashing and Salting Passwords: Why and How?

![Hashing](./images/hashing.png)

### Why Not Store Passwords Directly?
Storing passwords as plain text in a database is extremely risky. If someone gains access to the database (through hacking or internal misuse), they can see all user passwords. Since many people reuse passwords across sites, this can lead to widespread security breaches.

### What is Hashing?
Hashing is a process that transforms any input (like a password) into a fixed-size string of characters, which appears random. This is done using mathematical functions called hash functions. Key points:
- Hashing is one-way: you cannot reverse a hash to get the original password.
- The same input always produces the same hash (with the same algorithm and parameters).
- Hashes are always the same length, regardless of input size.

### How Does Hashing Help?
When a user creates a password, you hash it and store only the hash in the database. When they log in, you hash the password they enter and compare it to the stored hash. If they match, the password is correct. This way, the actual password is never stored or revealed.

### What is Salting?
A salt is a random string added to the password before hashing. This makes each hash unique, even if two users have the same password. Salting prevents attacks like "rainbow table" attacks, where hackers use precomputed tables to reverse hashes.

![Salt](./images/salt-hashing.png)

### How Does Bcrypt Work?
Bcrypt is a popular hashing algorithm for passwords. It:
- Adds a salt automatically.
- Allows you to set a "cost factor" (number of hashing rounds), making it slower and harder to crack.
- Stores the salt, cost, and hash together in one string.

![Bcrypt](./images/final-string.png)

### Why is This Secure?
- Even if someone gets the database, they only see hashes, not passwords.
- Salting ensures that identical passwords have different hashes.
- Bcrypt’s cost factor makes brute-force attacks much harder.

### Summary
- Never store plain text passwords.
- Always hash passwords, and use a salt.
- Use a strong algorithm like bcrypt, which handles salting and hashing securely.
- When verifying a password, hash the input and compare to the stored hash.

---
## Create Hashing Providers

### Why Use Providers for Hashing?


We start by creating an **abstract class** called `HashingProvider`. This class defines the contract for any hashing provider we want to use. It contains two abstract methods:

- `hashPassword(data: string | Buffer): Promise<string>`  
  Hashes the provided password and returns the hashed string.

- `comparePassword(data: string | Buffer, encrypted: string): Promise<boolean>`  
  Compares a plain password with a hashed password and returns `true` if they match.

By using an abstract class, we ensure that any concrete implementation (like bcrypt) must provide these methods.

#### Bcrypt Provider

Next, we create a **BcryptProvider** class that implements the `HashingProvider` abstract class. This class provides the actual logic for hashing and comparing passwords using the bcrypt algorithm.

- The `hashPassword` method uses bcrypt to hash the password.
- The `comparePassword` method uses bcrypt to compare a plain password with a hashed password.

This setup allows us to inject the `HashingProvider` wherever we need password hashing in our application. If we ever want to switch to a different algorithm (like Argon2), we just create a new provider that implements the same abstract class.

**Summary:**
- Create an abstract `HashingProvider` with `hashPassword` and `comparePassword` methods.
- Implement these methods in a concrete `BcryptProvider`.
- Use dependency injection to use the provider throughout the app.

This approach keeps our authentication logic clean, testable, and easy to update in the future.

[code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/b891ea81e1beb865cd9a5d68f5451e22d24acbc3)

---
## Implementing Hashing Provider

To implement the hashing provider using bcrypt, follow these steps:

### 1. Install the Bcrypt Package

First, install the bcrypt package in your project.  
It's recommended to use version `5.1.1` for compatibility:

```bash
npm install bcrypt@5.1.1
```

### 2. Import Bcrypt in Your Provider

In your `BcryptProvider` class, import bcrypt like this:

```typescript
import * as bcrypt from 'bcrypt';
```

### 3. Implement the `hashPassword` Method

- Mark the method as `async`.
- Generate a salt using `bcrypt.genSalt()`.
- Hash the password using `bcrypt.hash(data, salt)`.
- Return the hashed password.

Example:

```typescript
public async hashPassword(data: string | Buffer): Promise<string> {
  const salt = await bcrypt.genSalt();
  return bcrypt.hash(data, salt);
}
```

### 4. Implement the `comparePassword` Method

- Use `bcrypt.compare(data, encrypted)` to compare the plain password with the hashed password.
- Return the result (a boolean).

Example:

```typescript
public async comparePassword(data: string | Buffer, encrypted: string): Promise<boolean> {
  return bcrypt.compare(data, encrypted);
}
```

### Why Use Bcrypt?

- Bcrypt automatically handles salt generation and storage.
- It provides simple methods for hashing and comparing passwords.
- You don't need to manage salts or store them separately—bcrypt includes the salt in the hash string.

This makes password management secure and straightforward in your NestJS application.

[github code commit][](https://github.com/NadirBakhsh/nestjs-resources-code/commit/57ac9a496c49a65fbb4b4f22b54f502b3cac0c62)

---
## User Signup

### Hashing Passwords When Creating a New User

Now that we have our hashing provider and bcrypt implementation ready, the next step is to ensure that user passwords are hashed before being saved to the database.

#### Refactoring User Creation

Previously, the `createUser` method in the user service saved passwords as plain text. To improve security and code organization:

1. **Encapsulate User Creation Logic:**  
   Move the `createUser` method into a new provider called `CreateUserProvider` inside the users module. This keeps the user service clean and focused.

2. **Inject Dependencies:**  
   - Inject the `UsersRepository` using the `@InjectRepository` decorator.
   - Inject the `HashingProvider` using the `@Inject` decorator with `forwardRef`, since there is a circular dependency between the users and auth modules.

3. **Hash the Password:**  
   Before saving the user, use the hashing provider to hash the password:
   ```typescript
   const hashedPassword = await this.hashingProvider.hashPassword(createUserDto.password);
   ```

4. **Update User Service:**  
   Refactor the user service to delegate user creation to the new provider, further simplifying the service.

#### Testing the Implementation

- When you create a new user, the password will now be stored as a hash in the database, not as plain text.
- You can verify this by checking the users table after creating a user—the password field will contain a bcrypt hash.

This ensures all new users have their passwords securely hashed before storage.

[Github code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/0c95f022f7e83d4d28957c422b0f352e4d63348e)

---
## User SignIn Controller

### Implementing User Sign-In

To allow users to sign in, we need to:

1. **Create a SignIn DTO:**  
   - Create a `dtos` directory inside the `auth` module.
   - Add a `sign-in.dto.ts` file exporting a `SignInDto` class.
   - The class should have two properties: `email` (validated with `@IsEmail()` and `@IsNotEmpty()`) and `password` (validated with `@IsString()` and `@IsNotEmpty()`).

2. **Add a SignIn Endpoint in Auth Controller:**  
   - In `auth.controller.ts`, add a `@Post('sign-in')` endpoint.
   - The method should be `public async signIn(@Body() signInDto: SignInDto)`.
   - The method should call `this.authService.signIn(signInDto)`.

[Github code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/dfd62971e2399acea30bda284b0dea9fcb37d4b0)

---
## Completing the SignIn Method

### Encapsulating Sign-In Logic in a Provider

To keep the authentication logic modular and maintainable, encapsulate the sign-in process in a dedicated provider:

1. **Create a SignIn Provider:**  
   - Use the Nest CLI to generate a new provider: `sign-in.provider.ts` inside the `auth/providers` directory.
   - This provider will handle the full sign-in flow.

2. **Inject Dependencies:**  
   - Inject the `UsersService` using the `@Inject` decorator with `forwardRef` to handle circular dependencies.
   - Inject the `HashingProvider` for password comparison.

3. **Implement the Sign-In Method:**  
   - The method should accept a `SignInDto`.
   - Use the users service to find the user by email. If not found, an exception is thrown automatically.
   - Use the hashing provider's `comparePassword` method to compare the provided password with the stored hash.
   - If the passwords do not match, throw an `UnauthorizedException` with a message like "Incorrect password".
   - If the comparison fails due to an error, throw a `RequestTimeoutException`.
   - If the password matches, return a confirmation (e.g., `true`). Later, this will be replaced with a JWT token.

4. **Update Auth Service:**  
   - Inject the new sign-in provider into the auth service.
   - In the `signIn` method, delegate the logic to the sign-in provider.

5. **Update Auth Controller:**  
   - Ensure the controller's sign-in endpoint calls the updated auth service method.

This structure keeps your authentication logic clean, testable, and ready for future enhancements like JWT token generation.

[Github code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/e939e35af0ff231db5c876a62cae4f12aab02ade)

---
## Custom Response Code
### Setting the Correct HTTP Status for Sign-In

By default, NestJS returns a `201 Created` status code for successful POST requests. However, for a sign-in endpoint, the more appropriate status code is `200 OK`.

To customize the response code:

1. **Use the `@HttpCode` Decorator:**  
   - In your `auth.controller.ts`, add the `@HttpCode()` decorator above the sign-in endpoint method.
   - Import `HttpStatus` from `@nestjs/common` and set the code to `HttpStatus.OK` (which is 200).

   Example:
   ```typescript
   import { HttpCode, HttpStatus } from '@nestjs/common';

   @Post('sign-in')
   @HttpCode(HttpStatus.OK)
   public async signIn(@Body() signInDto: SignInDto) {
     return this.authService.signIn(signInDto);
   }
   ```

2. **Test the Endpoint:**  
   - When you send a valid sign-in request, you should now receive a `200 OK` response with the result (e.g., `true`).
   - If the password is incorrect, you should receive a `401 Unauthorized` response.

This ensures your API follows best practices for HTTP status codes, improving clarity for frontend and API consumers.

[Code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/3c374b9ddfa890cb5e8aa35785195e313e1842aa)

---
## Understanding JWTs

![JWT](./images/jwt.png)

### What is a JSON Web Token (JWT)?

A **JSON Web Token (JWT)** is an open industry standard (RFC 7519) for securely transmitting information between two parties as a JSON object. JWTs are commonly used for authentication and authorization in web applications.

A JWT consists of three parts, separated by periods (`.`):

1. **Header**  
   - Contains metadata about the token, such as the signing algorithm (e.g., HS256) and the token type (JWT).
   - Example (decoded):
     ```json
     {
       "alg": "HS256",
       "typ": "JWT"
     }
     ```

2. **Payload**  
   - Contains the claims or information you want to transmit (e.g., user ID, email).
   - The payload can be decoded by anyone, so **never put sensitive information here**.
   - Example (decoded):
     ```json
     {
       "sub": "1234567890",
       "email": "user@example.com"
     }
     ```

3. **Signature**  
   - Used to verify that the token has not been altered.
   - Created by encoding the header and payload, then signing them with a secret key known only to the server.
   - If the token is tampered with, the signature will not match and the token will be considered invalid.

**JWT Example:**  
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwiZW1haWwiOiJ1c2VyQGV4YW1wbGUuY29tIn0.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`

### How JWT Authentication Works

![Authentication Works](./images/AP.png)

1. **User Login:**  
   - The user sends their email and password to the server.
   - The server verifies the credentials.
   - If valid, the server generates a JWT and sends it back to the user.

2. **Accessing Protected Resources:**  
   - The client stores the JWT (usually in local storage or cookies).
   - For subsequent requests to protected endpoints, the client sends the JWT in the `Authorization` header.
   - The server verifies the JWT's signature and validity.
   - If valid, the server processes the request; otherwise, it returns an unauthorized error.

![Accessing Protected](./images/vp.png)

**Important Notes:**
- The payload of a JWT can be decoded by anyone; do not store sensitive data in it.
- The signature ensures the token's integrity and authenticity.
- JWTs have an expiration time (TTL). After expiration, the user must re-authenticate.

This mechanism allows stateless authentication: after login, the server does not need to store session data for each user. The JWT itself carries the necessary information and can be validated using the server's secret key.

---
## Adding JWT Configuration

### Installing and Configuring JWT in NestJS

To generate and verify JWT tokens in your NestJS application, follow these steps:

1. **Install the JWT Package**  
   Use npm to install the official NestJS JWT package.  
   It's recommended to use version `10.2.0` for compatibility:
   ```bash
   npm install @nestjs/jwt@10.2.0
   ```

2. **Add Environment Variables**  
   In your `.env.development` file, add the following variables:
   ```
   JWT_SECRET=your-random-secret-key
   TOKEN_AUDIENCE=http://localhost:3000
   TOKEN_ISSUER=http://localhost:3000
   ACCESS_TOKEN_TTL=3600
   ```
   - `JWT_SECRET`: Secret key for signing tokens (use a long, random string).
   - `TOKEN_AUDIENCE`: The intended audience (usually your app's URL).
   - `TOKEN_ISSUER`: The issuer of the token (usually your app's URL).
   - `ACCESS_TOKEN_TTL`: Token expiration time in seconds (e.g., 3600 for 1 hour).

3. **Create a JWT Config File**  
   Inside the `auth/config` directory, create a `jwt.config.ts` file:
   ```typescript
   import { registerAs } from '@nestjs/config';

   export default registerAs('jwt', () => ({
     secret: process.env.JWT_SECRET,
     audience: process.env.TOKEN_AUDIENCE,
     issuer: process.env.TOKEN_ISSUER,
     accessTokenTtl: parseInt(process.env.ACCESS_TOKEN_TTL ?? '3600', 10),
   }));
   ```

4. **Register Config and JWT Modules in Auth Module**  
   In your `auth.module.ts`:
   - Import `ConfigModule` and register the JWT config using `forFeature`.
   - Import `JwtModule` and register it asynchronously using the config provider.

   Example:
   ```typescript
   import { Module } from '@nestjs/common';
   import { ConfigModule } from '@nestjs/config';
   import jwtConfig from './config/jwt.config';
   import { JwtModule } from '@nestjs/jwt';

   @Module({
     imports: [
       ConfigModule.forFeature(jwtConfig),
       JwtModule.registerAsync(jwtConfig.asProvider()),
       // ...other imports...
     ],
     // ...existing code...
   })
   export class AuthModule {}
   ```

This setup ensures that your JWT configuration is modular, secure, and ready for use in the authentication flow. You can now inject the JWT service and configuration wherever needed in your auth module to generate and verify tokens.

---
## Generating JWT

### Using the JWT Service to Generate Tokens

Once the user successfully signs in, you can generate a JWT for them using the JWT service. Here's how:

1. **Inject the JWT Service:**  
   In your sign-in provider (`sign-in.provider.ts`), inject the `JwtService`:

   ```typescript
   import { JwtService } from '@nestjs/jwt';

   constructor(
     private readonly usersService: UsersService,
     private readonly hashingProvider: HashingProvider,
     private readonly jwtService: JwtService, // Inject JWT service
   ) {}
   ```

2. **Generate the JWT:**  
   In the sign-in method, after confirming the user's password is correct, generate the JWT:

   ```typescript
   const token = this.jwtService.sign(
     { email: user.email, sub: user.id }, // Payload
     { expiresIn: '1h' }, // Options
   );
   ```

   - The payload can contain any data you want to include in the token. Here, we're including the user's email and ID.
   - The `expiresIn` option sets the token's expiration time. You can also use the TTL from the config: `this.configService.get('jwt.accessTokenTtl')`.

3. **Return the JWT:**  
   Return the generated token from the sign-in method. The controller can then send this token to the client.

### Example: Complete Sign-In Flow with JWT

Here's how the complete sign-in flow looks with JWT integration:

```typescript
async signIn(signInDto: SignInDto) {
  // 1. Find the user by email
  const user = await this.usersService.findByEmail(signInDto.email);

  // 2. Check if the password matches
  const isPasswordValid = await this.hashingProvider.comparePassword(
    signInDto.password,
    user.password,
  );

  if (!isPasswordValid) {
    throw new UnauthorizedException('Incorrect password');
  }

  // 3. Generate the JWT
  const token = this.jwtService.sign(
    { email: user.email, sub: user.id },
    { expiresIn: '1h' },
  );

  // 4. Return the token
  return { access_token: token };
}
```

### Testing the JWT Generation

- After a successful sign-in, the response should now include a JWT token.
- This token can be used to authenticate subsequent requests to protected endpoints.

---
## JWT Token Signatures

### Understanding JWT Signatures

The signature is a crucial part of the JWT, ensuring its integrity and authenticity. Here's how it works:

1. **Signing the Token:**  
   When generating a JWT, the server creates a signature by combining the encoded header, encoded payload, and a secret key. This is done using the algorithm specified in the header (e.g., HS256).

2. **Verifying the Token:**  
   When a client presents a JWT, the server verifies the token by:
   - Reproducing the signature using the header, payload, and secret key.
   - Comparing the reproduced signature with the signature in the token.

   If both signatures match, the token is valid and unaltered. If not, the token is rejected.

### Example: HS256 Signature Generation

For the HS256 algorithm, the signature is generated using the HMAC SHA-256 hash function. Here's a simplified example:

```typescript
import * as crypto from 'crypto';

const header = '{"alg":"HS256","typ":"JWT"}';
const payload = '{"sub":"1234567890","email":"user@example.com"}';
const secret = 'your-256-bit-secret';

// 1. Encode Header and Payload
const encodedHeader = Buffer.from(header).toString('base64url');
const encodedPayload = Buffer.from(payload).toString('base64url');

// 2. Create Signature
const signature = crypto
  .createHmac('sha256', secret)
  .update(`${encodedHeader}.${encodedPayload}`)
  .digest('base64url');

// 3. Combine to Form JWT
const jwt = `${encodedHeader}.${encodedPayload}.${signature}`;
```

### Important Notes on Signatures
- The signature ensures that the token has not been tampered with. If any part of the token is altered, the signature will not match, and the token will be considered invalid.
- Never share your JWT secret key. It should be kept confidential and secure.
- For added security, regularly rotate your secret keys and implement key expiration and revocation mechanisms.

[Code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/a5c31b403d7f3d431cbe969db5f9e7560f9b8f09)

---
## Generating JWT

1. Injecting Dependencies

To generate a JWT token, you need two main dependencies in your provider’s constructor:

JwtService: Provided by @nestjs/jwt, used to sign and verify tokens.
JWT Configuration: Injected using NestJS’s config system, containing values like secret, audience, issuer, and token TTL.

```typescript
import { JwtService } from '@nestjs/jwt';
import { Inject } from '@nestjs/common';
import { ConfigType } from '@nestjs/config';
import jwtConfig from '../config/jwt.config';

constructor(
  private readonly jwtService: JwtService,
  @Inject(jwtConfig.KEY)
  private readonly jwtConfiguration: ConfigType<typeof jwtConfig>,
) {}
```

2. Generating the Token
After validating the user’s credentials, use jwtService.signAsync() to generate the token.

- **Payload**: Contains user info (e.g., sub for user ID, email).
- **Options**: Use values from your config (audience, issuer, secret, expiresIn).

```typescript
// ...existing code...
const accessToken = await this.jwtService.signAsync(
  {
    sub: user.id,
    email: user.email,
  },
  {
    audience: this.jwtConfiguration.audience,
    issuer: this.jwtConfiguration.issuer,
    secret: this.jwtConfiguration.secret,
    expiresIn: this.jwtConfiguration.accessTokenTtl,
  },
);
```

3. Returning the Token
Return the token as part of the response:

```typescript
return { access_token: accessToken };
```

**Summary**
- Inject JwtService and your JWT config.
- Use signAsync() to generate a token with user info and config options.
- Return the token to the client after successful sign-in.


[Code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/03685dcaaf11838dbecc9e496e73ca5dd24a94a8)

---
## JWT Token Signatures

![JWT Signatures](./images/jwt-signatures.png)

The signature is a crucial part of the JWT, ensuring its integrity and authenticity. Here's how it works:

1. **Signing the Token:**  
   When generating a JWT, the server creates a signature by combining the encoded header, encoded payload, and a secret key. This is done using the algorithm specified in the header (e.g., HS256).

2. **Verifying the Token:**  
   When a client presents a JWT, the server verifies the token by:
   - Reproducing the signature using the header, payload, and secret key.
   - Comparing the reproduced signature with the signature in the token.

   If both signatures match, the token is valid and unaltered. If not, the token is rejected.

**Example: HS256 Signature Generation**

For the HS256 algorithm, the signature is generated using the HMAC SHA-256 hash function. Here's a simplified example:

[Code commit](https://github.com/NadirBakhsh/nestjs-resources-code/commit/03685dcaaf11838dbecc9e496e73ca5dd24a94a8)

---