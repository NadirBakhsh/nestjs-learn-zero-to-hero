# Step 15 - Google Authentication

## Google Authentication Strategy
**Explanation:**  
Google authentication allows users to sign in to your application using their Google account. The process involves two main steps:

![front-end-work](./images/front-end-work.png)
1. **Frontend Initiation:**  
   - The user clicks "Sign in with Google" on your website.
   - The user is redirected to Google's authentication page, logs in, and grants permissions.
   - Google returns a JWT token (login ticket) to your frontend.

![authentication](./images/authentication.png)
2. **Backend Verification:**  
   - The frontend sends the Google JWT token to your NestJS API.
   - The API verifies the token using Google's client ID and secret.
   - If valid, the API checks if the user exists in the database:
     - If not, it creates a new user using the data from the Google token.
     - If yes, it uses the existing user.
   - The API generates and returns access and refresh tokens for the user.

**Benefits:**  
- Users can sign in without creating a new password.
- Secure and familiar authentication flow.
- Integrates seamlessly with your existing token-based authentication.

---

- Create Google Project
- Setting the Configuration
- Initialize Google Auth Client
- Implementation Strategy: Google Authentication
- Implement Authentication with Google Token
- React App in Front-End
- `createGoogleUser` Method
- Complete Google Authentication
- Testing Google Authentication