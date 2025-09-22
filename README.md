# Step 18 - Notification Emails

## Introduction to Notification Emails

![intro-email-notifications](./images/intro-email-notifications.png)
Notification emails are essential for keeping users informed about important events in your application, such as account creation, password resets, or updates. Until now, our application hasn't sent any notification emails—for example, new users haven't received a welcome email upon registration.

To develop and test notification emails safely, we'll use Mailtrap, an email testing service. Mailtrap provides its own SMTP credentials, which you can configure in your application. When a notification email (like a welcome email) is triggered, it is sent to Mailtrap instead of real users. You can then log in to Mailtrap's portal to view and test these emails. Mailtrap also offers tools to inspect and debug your emails.

![mailtrap-email](./images/testing-email-notification.png)

Once you're ready for production, you can replace Mailtrap's SMTP details with a live email service such as Amazon SES, SendGrid, or Sendinblue.

For templating email content, we'll use EJS (Embedded JavaScript Templates), which works well with NodeMailer—the underlying library used by NestJS's mailer module. This allows us to create dynamic and styled email templates. If you prefer, you can use other templating engines like Handlebars or Pug, but this course will focus on EJS.

In this section, we'll set up a welcome email that is sent to new users upon registration, configure Mailtrap, and integrate everything with NestJS's mailer module.

---

## Setup a Mailtrap Account

To test notification emails during development, we'll use a service called Mailtrap. Mailtrap offers a free tier that allows you to send up to 1000 test emails per month, making it convenient for most development needs.

To get started:
1. Sign up for a free account at [Mailtrap.io](https://mailtrap.io).
2. After registration, you'll be greeted with a dashboard for email testing.
3. Click on "Start Testing" to access your inbox and settings.
4. In the settings section, you'll find the SMTP credentials needed to configure your application.

Mailtrap's interface may change over time, but the key feature you need is "Email Testing" and the SMTP details. These credentials will allow your NestJS application to send emails to Mailtrap, where you can view and debug them without sending emails to real users.

You can use other SMTP servers for testing if you prefer, but Mailtrap is recommended for its ease of use and generous free tier. There are no affiliate links—this is simply a tool that works well for development.

## Configuration for Notification Emails

To enable notification emails in your NestJS application, follow these steps:

**1. Create the Mail Module and Service**
- Use the NestJS CLI to generate a new module:
  ```
  nest generate module mail --no-spec
  ```
- Generate the mail service inside the mail module:
  ```
  nest generate service mail --flat --no-spec
  ```
- The mail module will be automatically added to your app module. No controller is needed for the mail module, as it won't expose any endpoints.

**2. Install Required NPM Dependencies**
- Install the following packages:
  ```
  npm install @nestjs-modules/mailer@2.0.2 nodemailer@6.9.13 ejs@3.1.10
  ```
- `@nestjs-modules/mailer`: NestJS mailer integration.
- `nodemailer`: Underlying email sending library.
- `ejs`: Templating engine for email content.

**3. Add Environment Variables**
- In your `.env.development` file, add:
  ```
  MAIL_HOST=<your-mailtrap-host>
  SMTP_USERNAME=<your-mailtrap-username>
  SMTP_PASSWORD=<your-mailtrap-password>
  ```
  Replace values with those from your Mailtrap account.

**4. Update App Config**
- In `config/app.config.ts`, add properties for the new environment variables:
  ```typescript
  // ...existing code...
  mailHost: process.env.MAIL_HOST,
  smtpUsername: process.env.SMTP_USERNAME,
  smtpPassword: process.env.SMTP_PASSWORD,
  // ...existing code...
  ```

**5. Add Environment Variable Validations**
- In `config/environment.validation.ts`, add validations for the new variables:
  ```typescript
  // ...existing code...
  MAIL_HOST: Joi.string().required(),
  SMTP_USERNAME: Joi.string().required(),
  SMTP_PASSWORD: Joi.string().required(),
  // ...existing code...
  ```

**6. Create Templates Directory**
- Inside the `mail` module directory, create a `templates` folder.
- Add a sample template file named `welcome.ejs`:
  ```html
  <p>Welcome email</p>
  ```
  This will be used for the welcome email and can be customized later.

With these steps, your application is ready to send notification emails using Mailtrap and EJS templates.

## Configure NestJS Mailer

To configure the mailer module in your NestJS application:

**1. Make the Mail Module Global**
- In `mail.module.ts`, add the `@Global()` decorator to the `MailModule` class.
- This allows the mail service to be injected anywhere in your app without explicitly importing the mail module.

**2. Export the Mail Service**
- Add `MailService` to both the `providers` and `exports` arrays in `MailModule`.
- This makes the service available for dependency injection in other modules.

**3. Configure the Mailer Module**
- Import and configure `MailerModule` using `MailerModule.forRootAsync` in the `imports` array.
- Inject `ConfigService` and use a factory function to provide configuration options using environment variables.

Example configuration in `mail.module.ts`:
```typescript
import { Global, Module } from '@nestjs/common';
import { MailerModule } from '@nestjs-modules/mailer';
import { ConfigModule, ConfigService } from '@nestjs/config';
import { join } from 'path';
import { EjsAdapter } from '@nestjs-modules/mailer/dist/adapters/ejs.adapter';
import { MailService } from './mail.service';

@Global()
@Module({
  imports: [
    MailerModule.forRootAsync({
      inject: [ConfigService],
      useFactory: async (config: ConfigService) => ({
        transport: {
          host: config.get('app.mailHost'),
          port: 2525,
          secure: false,
          auth: {
            user: config.get('app.smtpUsername'),
            pass: config.get('app.smtpPassword'),
          },
        },
        defaults: {
          from: `"My Blog" <no-reply@nestjs-blog.com>`,
        },
        template: {
          dir: join(__dirname, 'templates'),
          adapter: new EjsAdapter(),
          options: {
            strict: false,
          },
        },
      }),
    }),
    ConfigModule,
  ],
  providers: [MailService],
  exports: [MailService],
})
export class MailModule {}
```

**Notes:**
- Ensure you import `join` from `path` and `EjsAdapter` from the correct location.
- The template directory should be set to the path where your EJS templates are stored.
- Update any misspelled environment variables in your config and validation files as needed.

With this setup, your mail service is globally available and ready to send emails using Mailtrap and EJS templates.

[Source Code](https://github.com/NadirBakhsh/nestjs-resources-code/commit/82c5b6966b497a9d59c877dd5a397b73cb3bde0d)

---

## Creating `MailService`

Now that the mailer module is configured, we can implement the mail service to send welcome emails to newly registered users.

**1. Inject the MailerService**
- In `mail.service.ts`, inject the `MailerService` using dependency injection:
  ```typescript
  import { Injectable } from '@nestjs/common';
  import { MailerService } from '@nestjs-modules/mailer';
  import { User } from '../user/entities/user.entity';

  @Injectable()
  export class MailService {
    constructor(private readonly mailerService: MailerService) {}
  }
  ```

**2. Create the Welcome Email Method**
- Add a method to send welcome emails:
  ```typescript
  public async sendUserWelcome(user: User): Promise<void> {
    await this.mailerService.sendMail({
      to: user.email,
      from: '"Onboarding Team" <support@nestjs-blog.com>',
      subject: 'Welcome to NestJS Blog',
      template: 'welcome',
      context: {
        name: user.firstName,
        email: user.email,
        loginUrl: 'http://localhost:3000',
      },
    });
  }
  ```

**3. Update the Welcome Template**
- Replace the content in `templates/welcome.ejs` with proper HTML:
  ```html
  <!DOCTYPE html>
  <html>
  <head>
    <title>Welcome Email</title>
  </head>
  <body>
    <h1>Welcome <%= name %>!</h1>
    <p>Thank you for registering with our NestJS Blog platform.</p>
    <p>You registered using the email address: <strong><%= email %></strong></p>
    <p>You can log in to your account using the following link:</p>
    <a href="<%= loginUrl %>">Login to your account</a>
    <p>We're excited to have you on board!</p>
  </body>
  </html>
  ```

**Key Features:**
- **Dependency Injection**: The `MailerService` is injected to handle email sending.
- **Template Variables**: Use EJS syntax `<%= variable %>` to insert dynamic content from the context.
- **Context Object**: Pass data like user name, email, and login URL to the template.
- **Template Override**: You can override default settings like the "from" address for specific emails.

The mail service is now ready to send welcome emails. The `sendUserWelcome` method can be called from other services (like the user service) when a new user registers.

[code source](https://github.com/NadirBakhsh/nestjs-resources-code/commit/29ed831287aa7279d85606ad831c5344a7aa3b73)

---

## Testing Email Service

Now that the mail service is implemented, let's test it by sending welcome emails to newly registered users.

**1. Integrate Mail Service with User Creation**
- In `create-user.provider.ts`, inject the `MailService` using dependency injection:
  ```typescript
  // ...existing code...
  constructor(
    // ...existing dependencies...
    private readonly mailService: MailService,
  ) {}
  ```

- Add email sending logic after user creation:
  ```typescript
  // ...existing code...
  // After saving the new user to database
  
  // Send welcome email
  try {
    await this.mailService.sendUserWelcome(newUser);
  } catch (error) {
    throw new RequestTimeoutException(error);
  }
  
  return newUser;
  ```

**2. Configure Static Assets for Templates**
- Templates need to be copied to the build directory. Update `nestjs-cli.json`:
  ```json
  {
    "$schema": "https://json.schemastore.org/nest-cli",
    "collection": "@nestjs/schematics",
    "sourceRoot": "src",
    "compilerOptions": {
      "deleteOutDir": true,
      "assets": [
        {
          "include": "mail/templates/**/*",
          "outDir": "dist"
        }
      ],
      "watchAssets": true
    }
  }
  ```

- The `assets` configuration ensures template files are copied to the `dist` directory during build.
- `watchAssets: true` enables automatic recompilation when template files change.

**3. Test the Email Service**
- Restart your NestJS application to apply the asset configuration.
- Verify that the `templates` directory exists in `dist/mail/templates/`.
- Create a new user using the POST `/users` endpoint.
- Check your Mailtrap inbox for the welcome email.

**4. Verify Email Content**
- The email should contain:
  - Dynamic user name from the context
  - User's email address
  - Login URL
  - Proper HTML structure

**Benefits of Global Mail Module:**
- No need to import `MailModule` in other modules
- `MailService` can be injected directly anywhere in the application
- Centralized email configuration and management

The email service is now fully functional, but the emails lack styling. This will be addressed in the next section.

---

## Enabling Inline CSS

To enhance the appearance of our emails, we can enable inline CSS. This ensures that our email templates look good across different email clients, which may have varying levels of support for CSS styles.

**1. Install Juice Package**
- Install the `juice` package, which inlines CSS styles into HTML:
  ```
  npm install juice
  ```

**2. Update Mailer Configuration**
- In `mail.module.ts`, update the mailer configuration to use `juice`:
  ```typescript
  import * as juice from 'juice';

  // ...existing code...

  template: {
    dir: join(__dirname, 'templates'),
    adapter: new EjsAdapter(),
    options: {
      strict: false,
      inlineCss: true, // Enable inline CSS
      inlineCssOptions: {
        url: 'http://localhost:3000', // Base URL for relative paths
      },
    },
  },
  ```

**3. Update Welcome Template with CSS**
- Modify `templates/welcome.ejs` to include CSS styles:
  ```html
  <!DOCTYPE html>
  <html>
  <head>
    <title>Welcome Email</title>
    <style>
      body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
      .container { max-width: 600px; margin: 0 auto; padding: 20px; }
      h1 { color: #333; }
      p { line-height: 1.5; }
      a { color: #007bff; text-decoration: none; }
      a:hover { text-decoration: underline; }
    </style>
  </head>
  <body>
    <div class="container">
      <h1>Welcome <%= name %>!</h1>
      <p>Thank you for registering with our NestJS Blog platform.</p>
      <p>You registered using the email address: <strong><%= email %></strong></p>
      <p>You can log in to your account using the following link:</p>
      <a href="<%= loginUrl %>">Login to your account</a>
      <p>We're excited to have you on board!</p>
    </div>
  </body>
  </html>
  ```

[code source](https://github.com/NadirBakhsh/nestjs-resources-code/commit/cb387fdd22f54643d2820e67d248968a97eb893c)

---
**4. Test the Email with Inline CSS**
- Repeat the user creation and email testing steps.
- Check the email in Mailtrap and verify the CSS styles are applied.

Enabling inline CSS improves the email's appearance, making it more visually appealing and easier to read for users.

---

## Conclusion

In this step, we implemented notification emails in our NestJS application. We configured Mailtrap for safe email testing, set up the mailer module, and created a mail service to send welcome emails to new users.

We also learned how to integrate the mail service with user creation, configure static assets for email templates, and enable inline CSS for better email styling.

With these features, our application can now send well-formatted notification emails to users, enhancing user engagement and providing important updates.

In the next steps, we will continue to improve our application by adding more features and refining the existing ones. Stay tuned!