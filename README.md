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

## Create Google Project

**Steps:**
1. Go to [Google Cloud Console](https://console.cloud.google.com) and sign in.
2. Create a new project (e.g., "Nestjs auth").
3. In the sidebar, go to "APIs & Services" > "OAuth consent screen".
   - Select "External" user type.
   - Fill in required fields (app name, support email, developer contact).
   - Add scopes for user info and profile.
   - Add test users (Gmail addresses).
   - Save and continue through the steps.
4. Go to "Credentials" and create new credentials:
   - Choose "OAuth client ID".
   - Select "Web application".
   - Add authorized JavaScript origins (e.g., `http://localhost:3500` and `http://localhost`).
   - Click "Create" to get your client ID and client secret.
5. Save the client ID and secret for use in your application.

**Result:**  
You now have a Google project and OAuth credentials ready for implementing Google authentication in your app.

---

- Setting the Configuration
- Initialize Google Auth Client
- Implementation Strategy: Google Authentication
- Implement Authentication with Google Token
- React App in Front-End
- `createGoogleUser` Method
- Complete Google Authentication
- Testing Google Authentication