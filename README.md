# Step 17 - File Uploads

## Introduction to File Uploads

![multipart-form-data](./images/multipart-form-data.png)

In this section, you'll learn how to handle file uploads in NestJS. Unlike regular JSON requests, file uploads use the `multipart/form-data` content type. NestJS provides a `FileInterceptor` to help extract files from incoming requests, and a special decorator to access the uploaded file in your controller (similar to how you use `@Body()` for JSON data).

![upload-actions](./images/upload-actions.png)

The typical flow is:
- The client sends a POST request with a file using `multipart/form-data`.
- The controller uses the `FileInterceptor` and file decorator to access the uploaded file.
- The file is then uploaded to an Amazon S3 bucket.
- S3 returns a URL for the file, which is often served through Amazon CloudFront (a CDN for faster access).
- Information about the file and its CloudFront URL is saved in your database.

CloudFront helps deliver files quickly to users by caching them in multiple locations around the world, reducing latency and improving user experience.

![distribution-with-cdn](./images/distribution-with-cdn.png)

This section will guide you through each step, from receiving the file in your API to storing and serving it efficiently.

## Setup S3 and CloudFront
---
## Uploads Module and Configuration
---
## Create Upload Entity
---
## Upload File Service and Controller
---
## `UploadToAwsProvider`
---
## Complete Uploads Service
---
## Testing File Uploads