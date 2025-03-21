# Step 05 - Documenting Code

## 1. Documentation with NestJS

![Documentation Image](./images/Documentation-nestjs.png)

### Introduction

NestJS provides built-in tools for documenting both code and API endpoints. This documentation helps both developers and clients interact with the system efficiently.

### Types of Documentation

NestJS supports two primary types of documentation:

1. **API Documentation** (for developers and clients)
2. **Code Documentation** (for developers)

![Documentation-types](./images/Documentation-types.png)

### 1. API Documentation with OpenAPI (Swagger)

NestJS leverages **OpenAPI (Swagger)** to document APIs. This allows developers and clients to explore API endpoints, understand request/response structures, and even test APIs interactively.

#### Features:

- Automatically generated documentation based on decorators.
- Hosted at `localhost:3000/api` (by default).
- Documents modules, controllers, routes, query parameters, and request/response bodies.
- Interactive **Try Out** feature to test API requests.

#### Example:

```typescript
import { DocumentBuilder, SwaggerModule } from "@nestjs/swagger"

const config = new DocumentBuilder()
  .setTitle("API Documentation")
  .setDescription("API description")
  .setVersion("1.0")
  .build()
const document = SwaggerModule.createDocument(app, config)
SwaggerModule.setup("api", app, document)
```

### 2. Code Documentation with Compodoc

**Compodoc** generates documentation for the internal structure of a NestJS application, providing an overview of modules, services, controllers, and dependencies.

#### Features:

- Provides a detailed map of modules and their dependencies.
- Displays methods, properties, and class structures.
- Helps track code coverage for documentation completeness.

#### Example Usage:

```bash
npm install -g @compodoc/compodoc
compodoc -p tsconfig.json -s
```

### Key Differences

| Feature  | API Documentation (Swagger) | Code Documentation (Compodoc)    |
| -------- | --------------------------- | -------------------------------- |
| Audience | Developers & Clients        | Developers Only                  |
| Purpose  | Describe API Endpoints      | Describe Internal Code Structure |
| Hosting  | Hosted with API             | Generated locally                |
| Format   | OpenAPI Specification       | HTML-based Documentation         |

### Conclusion

NestJS simplifies both API and code documentation:

- **Swagger** helps document and test APIs interactively.
- **Compodoc** provides an internal view of the codebase for developers.
  By leveraging these tools, developers can maintain well-structured, self-explanatory applications.

---

## 2. Open API Specification

![OpenAPI-Specification](./images/open-api-specification.png)

### What is OpenAPI Specification?

OpenAPI Specification (OAS) is a standardized format for describing REST APIs. It defines how an API should be documented, ensuring consistency across different platforms and tools.

### What is Swagger?

Swagger UI: A user-friendly interface for exploring and testing APIs.

---

## 3. Enabling Swagger in NestJS

![Enabling-Swagger](./images/enabling-swagger.png)

### 1. Install Swagger

Run the following command to install a specific version of `@nestjs/swagger`:

```sh
npm install @nestjs/swagger@<version>
```

### 2. Import Required Modules

Open `main.ts` and import the required modules:

```ts
import { DocumentBuilder, SwaggerModule } from "@nestjs/swagger"
```

### 3. Configure Swagger

#### Create a Swagger config using `DocumentBuilder`:

```ts
const config = new DocumentBuilder()
  .setTitle("API Documentation")
  .setDescription("NestJS Swagger API documentation")
  .setVersion("1.0")
  .build()
```

#### Generate a Swagger document using `SwaggerModule.createDocument`:

```ts
const document = SwaggerModule.createDocument(app, config)
```

#### Setup Swagger in the app using `SwaggerModule.setup`:

```ts
SwaggerModule.setup("api", app, document)
```

This will expose the Swagger UI at **`http://localhost:3000/api`**.

### 4. Run the App & Access Swagger

- Start your NestJS app:
  ```sh
  npm run start
  ```
- Open **`http://localhost:3000/api`** in your browser to view the API documentation.

code example [Enabling-Swagger](https://github.com/NadirBakhsh/nestjs-resources-code/commit/1ed8ae48b12074490b135827a5d2e77eecba5ab7)

---

## 4. Adding Configuration Methods to Swagger

![Adding-Configuration-Methods-to-Swagger](./images/methods-to-add-swagger.png)

### Adding Basic Information

#### Set Title

```ts
const config = new DocumentBuilder()
  .setTitle("NestJS Master Class Blog App API")
  .build()
```

#### Set Description

```ts
.setDescription('Use API URL as http://localhost:3000')
```

#### Set Terms of Service

```ts
.setTermsOfService('https://example.com/terms')
```

#### Set License

```ts
.setLicense('MIT', 'https://opensource.org/licenses/MIT')
```

#### Adding Server Configuration

```ts
.addServer('http://localhost:3000')
```

#### Organizing API Endpoints with Tags

By default, all endpoints are under a general collapsible. To organize them into meaningful groups, we use @ApiTags.

```ts
@ApiTags("users")
@Controller("users")
export class UsersController {}
```

```ts
@ApiTags("posts")
@Controller("posts")
export class PostsController {}
```

code example [Adding-Configuration-Methods-to-Swagger](https://github.com/NadirBakhsh/nestjs-resources-code/commit/0c7ae75b0253d4effd4723c4f7ef273cb62e143d)

---

## 5. Documenting GET Users

![Documenting-GET-Users](./images/doc-get-users.png)

### Steps to Document API with Swagger

#### 1. Adding Swagger Decorators

Swagger decorators can be used within **DTOs** and **controllers** to define API documentation.

#### 1.1 Documenting Parameters in DTOs

To document an `ID` parameter:

```typescript
import { ApiPropertyOptional } from "@nestjs/swagger"

export class GetUsersParamDTO {
  @ApiPropertyOptional({
    description: "Get user with a specific ID",
    example: 1234
  })
  id: number
}
```

- `ApiPropertyOptional` marks the property as optional.
- `description` provides details about the parameter.
- `example` gives an example value.

#### 1.2 Documenting Query Parameters in Controllers

Query parameters are documented using `@ApiQuery`.

```typescript
import { ApiQuery } from '@nestjs/swagger';

@ApiQuery({
  name: 'limit',
  type: Number,
  required: false,
  description: 'Number of entries returned per query',
  example: 10,
})
@ApiQuery({
  name: 'page',
  type: Number,
  required: false,
  description: 'Page number to return',
  example: 1,
})
```

- `name` specifies the query parameter.
- `type` defines its data type.
- `required: false` makes it optional.
- `description` and `example` provide additional details.

#### 2. Adding API Endpoint Descriptions

Use `@ApiOperation` to provide a summary of the API.

```typescript
import { ApiOperation } from '@nestjs/swagger';

@ApiOperation({ summary: 'Fetches a list of registered users.' })
```

#### 3. Documenting API Responses

Use `@ApiResponse` to specify the possible API responses.

```typescript
import { ApiResponse } from '@nestjs/swagger';

@ApiResponse({
  status: 200,
  description: 'Users fetched successfully based on the query.',
})
```

- You can add multiple `@ApiResponse` decorators for different response statuses.

### Testing in Swagger UI

Once decorated, Swagger UI will automatically generate interactive documentation where developers can test the API using predefined example values.

### Conclusion

By using Swagger decorators in DTOs and controllers, we can create well-structured, interactive API documentation, making it easier for developers to understand and use our APIs effectively.

Code example [Documenting-GET-Users](https://github.com/NadirBakhsh/nestjs-resources-code/commit/ea19f3ffbaa852c545eaaf1032952e7ccd4e8dda)

---

## 6. Practice: POST Endpoint and DTO for Posts Controller

```bash
/**
 * Structure of request body to create a new post
 ******

 * title: string

 * postType: enum (post, page, story, series)

 * slug: string

 * status: enum (draft, scheduled, review, published)

 * content?: string 

 * schema?: string

 * featuredImageUrl?: string

 * publishOn: Date

 * tags: string[]

 * metaOptions: [{key:value}]
 */
```

---

## 7. Solution: POST Endpoint and DTO for Posts Controller

### Commit Summary: Solution - POST Endpoint and DTO for Posts Controller

### ✅ Commit: [c18d878](https://github.com/NadirBakhsh/nestjs-resources-code/commit/c18d878732d70e36c88a3af9f07aa8d1de910cc5)

### 🔍 Key Changes Introduced:

#### 1. CreatePostDto DTO Added
- **File:** `src/posts/dtos/createPost.dto.ts`
- **Fields:**
  - `title: string`
  - `postType: postType`
  - `slug: string`
  - `status: postStatus`
  - `content?: string`
  - `schema?: string`
  - `featuredImageUrl?: string`
  - `publishOn?: Date`
  - `tags: string[]`
  - `metaOptions: [{ key: 'sidebarEnabled', value: true }]`

#### 2. Enums Introduced
- **File:** `src/posts/enums/postStatus.enum.ts`
  - `postStatus`
    - `DRAFT = 'draft'`
    - `SCHEDULED = 'scheduled'`
    - `REVIEW = 'review'`
    - `PUBLISHED = 'published'`

- **File:** `src/posts/enums/postType.enum.ts`
  - `postType`
    - `POST = 'post'`
    - `PAGE = 'page'`
    - `SERIES = 'series'`
    - `STORY = 'story'`

#### 3. Posts Controller Updated
- **File:** `src/posts/posts.controller.ts`
- **Changes:**
  - New POST endpoint added: `@Post()`
  - Accepts: `@Body() createPostDto: CreatePostDto`
  - Controller logic pending implementation

### 📁 Files Changed:
- `src/posts/dtos/createPost.dto.ts` ➕ Added
- `src/posts/enums/postStatus.enum.ts` ➕ Added
- `src/posts/enums/postType.enum.ts` ➕ Added
- `src/posts/posts.controller.ts` ✍ Modified

### 🔗 Preview:
- [Reader View of Commit](https://r.1lm.io/p/https://github.com/NadirBakhsh/nestjs-resources-code/commit/c18d878732d70e36c88a3af9f07aa8d1de910cc5)

---

## 8. Adding Validations to CreatePostDto

#### 1. Enhanced Validation for `CreatePostDto`
- **File:** `src/posts/dtos/createPost.dto.ts`
- Added `class-validator` decorators to validate incoming data:

| Property | Validation Decorators |
|---------|-----------------------|
| `title` | `@IsString()`, `@MinLength(3)`, `@IsNotEmpty()` |
| `postType` | `@IsEnum(postType)`, `@IsNotEmpty()` |
| `slug` | `@IsString()`, `@IsNotEmpty()`, `@Matches()` (slug pattern validation) |
| `status` | `@IsEnum(postStatus)`, `@IsNotEmpty()` |
| `content` | `@IsString()`, `@IsOptional()` |
| `schema` | `@IsOptional()`, `@IsJSON()` |
| `featuredImageUrl` | `@IsOptional()`, `@IsUrl()` |
| `publishOn` | `@IsISO8601()`, `@IsOptional()` |
| `tags` | `@IsOptional()`, `@IsArray()`, `@IsString({ each: true })`, `@MinLength(3, { each: true })` |
| `metaOptions` | Custom structure — no decorators yet |

### 📁 File Changed:
- `src/posts/dtos/createPost.dto.ts` ✍ Modified

### 🔗 Preview:
- [Reader View of Commit](https://r.1lm.io/p/https://github.com/NadirBakhsh/nestjs-resources-code/commit/cf24b05bc142e60c99f1b9ed9a8a6ff405f248e5)

---

## 9. Working with Nested DTOs

### Description
Added support for **nested DTOs** in post creation logic. Enhancements enable structured, validated data for nested `metaOptions` fields inside `CreatePostDto`.

### Key Changes

### 📄 File: `src/posts/dtos/createPost.dto.ts`
- ✅ Added `metaOptions: CreatePostMetaOptionsDto[]` field.
- ✅ Applied `@ValidateNested({ each: true })`, `@Type()` for transformation.
- ✅ Enhanced validation using class-validator decorators.

### 📄 File: `src/posts/dtos/createPostMetaOptions.dto.ts`
- ✅ New DTO class `CreatePostMetaOptionsDto`.
- ✅ Fields:
  - `key: string` → Validated with `@IsString()` and `@IsNotEmpty()`
  - `value: any` → Validated with `@IsNotEmpty()`

#### Purpose
To enable scalable, clean nested object validation in incoming requests (e.g., meta data config for posts).

#### Benefits
- 🔐 Strong type-checking and input validation
- 📦 Cleaner and reusable DTO structure
- 🚀 Easier expansion for more nested fields in future

#### Link
[🔗 View Commit on GitHub](https://github.com/NadirBakhsh/nestjs-resources-code/commit/acc07153510e45954ff26ad73d38f22c307e0a15)

---

## 10. Testing Validation

#### check result on terminal

![post-api-test](./images/post-api-test.png)

[code link](https://github.com/NadirBakhsh/nestjs-resources-code/commit/6dc64ff54f257a314fda631cc656995954246547)

---

## 11. Using Swagger for Documenting CreatePostDto

### Description
Integrated **Swagger decorators** into `CreatePostDto` class to enable automated API documentation.

### Key Changes

### 📄 File: `src/posts/dtos/createPost.dto.ts`
- ✅ Added `@ApiProperty()` and `@ApiPropertyOptional()` decorators for all fields.
- ✅ Included descriptions and example values.
- ✅ Documented nested DTO `metaOptions` in Swagger schema.

### Benefits
- 📚 Improved API documentation in Swagger UI.
- ✅ Enhances developer experience and onboarding.
- 🔍 Clear visibility into expected request payload structure.

#### Example Documented Fields
- `title`, `postType`, `slug`, `status`, `tags`, `schema`, `metaOptions`, `featuredImageUrl`, `publishOn`

#### Link
[🔗 View Commit on GitHub](https://github.com/NadirBakhsh/nestjs-resources-code/commit/cce95c29af500d09eeb3fd4e25896aca44167d82)

![Swagger Api DOC](./images/doc-screen-shot.png)
---

## 12. Mapped Types Using Swagger

## ✅ Update Summary: Mapped Types Using Swagger

### Highlights:
- **Added `PatchPostDto` using `PartialType(CreatePostDto)` for PATCH operations.**
  - Utilized `@nestjs/swagger` and `class-validator`.
  - Applied `@ApiProperty`, `@IsInt`, `@IsNotEmpty` for `id` field.

- **New HTTP Endpoint Definition:**
  - Added `src/posts/htttp/patch.post.endpoints.http` with `PATCH /posts` example.

- **Controller Enhancements (`PostsController`):**
  - Added `@Patch()` route handler for updating posts.
  - Added `@ApiOperation` and `@ApiResponse` decorators for Swagger UI enhancement.

### Affected Files:
- `src/posts/dtos/patch-post-dto.ts` – New DTO for partial updates.
- `src/posts/htttp/patch.post.endpoints.http` – Manual test request file.
- `src/posts/posts.controller.ts` – Added `updatePost()` endpoint with Swagger docs.

**Code Commit:** [`Code view`](https://github.com/NadirBakhsh/nestjs-resources-code/commit/e9a444b6572a8122fb66d438e6cb43a877b1c505)  

---

## 13. Getting Started with Compodoc

![compodoc](./images/compodoc.png)

**Config**

1. Install Compodoc as a dev dependency (`@compodoc/compodoc`).
2. Add documentation scripts in package.json.
3. Run documentation generator via CLI.

```JSON
// package.json (partial snippet)
{
  "devDependencies": {
    "@compodoc/compodoc": "^1.1.21" // or latest
  },
  "scripts": {
    "doc": "npx @compodoc/compodoc -p tsconfig.json -s --port 3001 --watch -d ./documentation"
  }

}
```
[code link](https://github.com/NadirBakhsh/nestjs-resources-code/commit/d4a4c800c549a37e438d47803f877a0fcd29ac57)

---

## 14. Compodoc Coverage and JSDocs
Compodoc coverage is an additional feature for calculating the percentage of documented components.

#### How it works
1. Compodoc analyzes all the components, modules, controllers, services, etc. in your project.
2. It then checks which of these components have comments.
3. The percentage of commented components is then calculated.

This feature is useful for enforcing code quality and ensuring that all components are documented.

[JSDocs](https://jsdoc.app/) is a format for documenting JavaScript code. It is used by Compodoc to generate documentation.
