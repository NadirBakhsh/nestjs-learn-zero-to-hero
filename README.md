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

## Configure NestJS Mailer

## Creating `MailService`

## Testing Email Service

## Enabling Inline CSS