# Step 14 - Refresh Tokens

## Introduction to Refresh Tokens

![intro](./images/intro.png)

**Explanation:**  
Access tokens are short-lived tokens used to authenticate users. Because they expire quickly (e.g., after 1 hour), they help keep your application secure by limiting the time an attacker can use a stolen token.

**Refresh tokens** are longer-lived tokens (e.g., 1 day) that allow users to get new access tokens without logging in again. When an access token expires, the frontend uses the refresh token to request a new access token from the backend.

- **Access tokens**: Used for authentication, short lifespan, more sensitive payload.
- **Refresh tokens**: Used only to get new access tokens, longer lifespan, minimal payload (usually just user ID).

![how-work](./images/how-work.png)

**How it works:**
1. When a user signs in, both an access token and a refresh token are returned.
2. The frontend stores both tokens (in local storage or cookies).
3. When the access token expires, the frontend sends the refresh token to the backend to get a new access token.
4. This process repeats until the refresh token expires, at which point the user must log in again.

**Benefits:**  
- Improved security: Short-lived access tokens reduce risk if stolen.
- Better user experience: Users stay logged in without frequent re-authentication.

---

## Refresh Token Configuration

**Steps:**
1. **Add Environment Variable:**  
   - In your `.env` or environment file, add:
     ```
     JWT_REFRESH_TOKEN_TTL=86400
     ```
     (Represents 24 hours in seconds.)

2. **Add Validation:**  
   - In `src/config/environment.validation.ts`, add validation for `JWT_REFRESH_TOKEN_TTL` (e.g., required, number).

3. **Update JWT Config:**  
   - In `src/auth/config/jwt.config.ts`, add a property for `refreshTokenTtl`:
     ```typescript
     refreshTokenTtl: parseInt(process.env.JWT_REFRESH_TOKEN_TTL ?? '86400', 10),
     ```

4. **Create DTO for Refresh Token:**  
   - In `src/auth/dto/refresh-token.dto.ts`, create:
     ```typescript
     export class RefreshTokenDto {
       @IsNotEmpty()
       @IsString()
       refreshToken: string;
     }
     ```
   - This DTO will be used to receive the refresh token from the client when requesting new tokens.

[code example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/6eff3ffee9799e37a6efaf799a9e59f445ee39f8)

---

## Generate Tokens Provider

## Generate Tokens Method

## Generate Refresh Token on SignIn

## Generate Access Token Using Refresh Token

## Create Refresh Token Endpoint