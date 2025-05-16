# Step 07 - Database Relations


## Relationships in SQL Database
![Relationships in DQL Databases](./images/Relationships%20in%20SQL%20Databases.png)

# Relational Database Relationships Overview with TypeORM & NestJS

### 📘 Introduction
In this section, we explore the **types of relationships in relational databases**, and how to define them using **TypeORM** in a **NestJS** application.

### 🔍 Why Use Relational Databases?
Relational databases such as **PostgreSQL** and **MySQL** are powerful systems that manage data through relationships between tables.

![alt text](./images/relational%20databases.png)

### ✅ Key Advantages:
1. **Avoid Data Duplication**  
   - Reduce redundancy by separating data into multiple related tables.
   - Example: Separate tables for `Users`, `Posts`, `Profiles`, etc.

2. **Ensure Data Accuracy**  
   - Enforce column constraints (e.g., data types, required fields).
   - Example: A `timestamp` column will always contain a valid timestamp due to validation rules.

3. **Flexibility & Scalability**  
   - Start with small schemas and grow your database as your application scales.
   - Easy to add more tables and relationships later.


### 🔗 Types of Relationships in Relational Databases
![one-to-one](./images/one-to-one.png)

### 1️⃣ One-to-One (1:1)
- Each row in Table A has **only one** related row in Table B and vice versa.
- **Example:** `Post` ⇨ `MetaOptions`
  - Each post has **one meta option** (e.g., JSON settings).
  - Stored in a separate `MetaOptions` table.

![one-to-many](./images/one-to-many.png)
### 2️⃣ One-to-Many (1:N) / Many-to-One (N:1)
- **One-to-Many**: One row in Table A relates to **multiple** rows in Table B.
- **Many-to-One**: Multiple rows in Table B relate back to **one** row in Table A.
- **Example:** `Author` ⇨ `Posts`
  - One author can write multiple posts (1:N).
  - Each post has one author (N:1).

![many-to-many](./images/many-to-many.png)
### 3️⃣ Many-to-Many (M:N)
- Multiple rows in Table A relate to multiple rows in Table B.
- **Example:** `Posts` ⇄ `Tags`
  - One post can have many tags (e.g., JavaScript, TypeScript).
  - One tag can be assigned to many posts.

---

### 🛠️ Summary
| Relationship Type | Description                              | Example                    |
|------------------|------------------------------------------|----------------------------|
| One-to-One       | One record in A relates to one in B      | Post ⇨ MetaOptions         |
| One-to-Many      | One record in A relates to many in B     | Author ⇨ Posts             |
| Many-to-One      | Many records in B relate to one in A     | Posts ⇨ Author             |
| Many-to-Many     | Many records in A relate to many in B    | Posts ⇄ Tags               |

---
## 📌 What’s Next
In the upcoming sections, we’ll learn **how to implement each relationship type** using **TypeORM decorators** inside a **NestJS** project.
Stay tuned!

---

## Creating the Tags Entity

![alt text](./images/Creating%20the%20Tags%20Entity.png)


#### Files Added/Modified:

1. `src/app.module.ts`
- Registered `TagsModule by file path is not` into the main application module.

2. `src/tags/tag.entity.ts`
- Created `Tag` entity with the following fields:
  - `id`: Primary key
  - `name`: Unique, non-null, max length 256
  - `slug`: Unique, non-null, max length 256
  - `description`: Optional text
  - `schema`: Optional text
  - `featuredImageUrl`: Optional varchar(1024)
  - `createDate`, `updateDate`, `deletedDate`: Timestamps

3. `src/tags/tags.controller.ts`
- Empty controller scaffold for future Tag routes.

4. `src/tags/tags.module.ts`
- Tag feature module created, controller registered.

#### Notes:
- Modular architecture pattern followed.
- Entity includes soft delete support via `DeleteDateColumn`.
- No service or repository layer created yet; controller is also empty.
- Future enhancements expected: CRUD endpoints, DTOs, validation, services.

#### Recommendations:
- Implement `TagsService` for business logic.
- Add input validation via DTOs.
- Add unit tests for the entity schema.

 Code: [View full commit](https://r.1lm.io/p/https://github.com/NadirBakhsh/nestjs-resources-code/commit/eb604d2b49a19fe3b56de4699fc899fc40a0650d)

---
## Creating the Meta Options Entity
![alt text](./images/Creating%20the%20Meta%20Options%20Entity.png)

## MetaOptions Module

### Description
This module introduces a new `MetaOption` entity designed to store dynamic JSON meta values in the database.

### Entity Structure
| Property     | Type   | Description                     |
|--------------|--------|---------------------------------|
| id           | number | Primary Key                     |
| metaValue    | json   | JSON structured metadata field  |
| createDate   | Date   | Auto timestamp on create        |
| updateDate   | Date   | Auto timestamp on update        |

### Module Structure
- `meta-option.entity.ts`: Entity definition
- `meta-options.controller.ts`: REST Controller (base path: `/meta-options`)
- `meta-options.module.ts`: Module declaration

### Files Added/Updated
- `src/app.module.ts` – Imported `MetaOptionsModule`
- `src/meta-options/meta-option.entity.ts` – Entity class `MetaOption`
- `src/meta-options/meta-options.controller.ts` – Controller definition
- `src/meta-options/meta-options.module.ts` – Module declaration

### Entity Code
```ts
// src/meta-options/meta-option.entity.ts

import {
  Column,
  CreateDateColumn,
  Entity,
  PrimaryGeneratedColumn,
  UpdateDateColumn,
} from 'typeorm';

@Entity()
export class MetaOption {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({
    type: 'json',
    nullable: false,
  })
  metaValue: string;

  @CreateDateColumn()
  createDate: Date;

  @UpdateDateColumn()
  updateDate: Date;
}
```

### Integration
Ensure `MetaOptionsModule` is imported into `AppModule`:

```ts
import { MetaOptionsModule } from './meta-options/meta-options.module';

@Module({
  imports: [MetaOptionsModule, ...]
})
```

---
## Updating DTO Files


### Description
This commit introduces updates and refactors to Data Transfer Object (DTO) structures across multiple modules.

### Files Added/Updated
- `src/meta-options/dtos/createPostMetaOptions.dto.ts` – ➕ New DTO for `metaValue` field validation.
- `src/posts/dtos/createPost.dto.ts` – 🔁 Updated import path for `CreatePostMetaOptionsDto`.
- `src/posts/dtos/createPostMetaOptions.dto.ts` – ❌ Removed duplicate DTO file.
- `src/posts/post.entity.ts` – 🔁 Adjusted import path of DTO.
- `src/tags/dtos/create-tag.dto.ts` – ➕ New tag creation DTO with detailed validations.

### DTO: `CreatePostMetaOptionsDto`
```ts
import { IsJSON, IsNotEmpty } from 'class-validator';

export class CreatePostMetaOptionsDto {
  @IsNotEmpty()
  @IsJSON()
  metaValue: string;
}
```

### DTO: `CreateTagDto`
```ts
import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';
import {
  IsJSON,
  IsNotEmpty,
  IsOptional,
  IsString,
  IsUrl,
  Matches,
  MaxLength,
  MinLength
} from 'class-validator';

export class CreateTagDto {
  @ApiProperty()
  @IsString()
  @IsNotEmpty()
  @MinLength(3)
  @MaxLength(256)
  name: string;

  @ApiProperty({
    description: 'Here it is the slug for the blog post',
    example: 'post-url-slug',
  })
  @IsString()
  @IsNotEmpty()
  @MaxLength(256)
  @Matches(/^[A-Za-z0-9]+(?:-[A-Za-z0-9]+)*$/, {
    message: 'Slug must be a valid slug',
  })
  slug: string;

  @ApiPropertyOptional()
  @IsOptional()
  @IsString()
  description?: string;

  @ApiPropertyOptional()
  @IsOptional()
  @IsJSON()
  schema?: string;

  @ApiPropertyOptional()
  @IsOptional()
  @IsUrl()
  @MaxLength(1024)
  featuredImageUrl?: string;
}
```
## DTO Updates Commit Summary

### Commit Ref: [Updating DTO Files](https://github.com/NadirBakhsh/nestjs-resources-code/commit/906a80e5645041d829e534cb7b9d4729952afabf)

---
## Autoloading Entities

### Description
Initial implementation of the MetaOptions module including entity, controller, and module integration.

### Files Added/Updated
- `src/app.module.ts` – Imported `MetaOptionsModule` into AppModule.
- `src/meta-options/meta-option.entity.ts` – New `MetaOption` entity class created.
- `src/meta-options/meta-options.controller.ts` – Basic REST controller with base route `/meta-options`.
- `src/meta-options/meta-options.module.ts` – Declared `MetaOptionsModule` and linked controller.

### Entity Code: `MetaOption`
```ts
import {
  Column,
  CreateDateColumn,
  Entity,
  PrimaryGeneratedColumn,
  UpdateDateColumn,
} from 'typeorm';

@Entity()
export class MetaOption {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({
    type: 'json',
    nullable: false,
  })
  metaValue: string;

  @CreateDateColumn()
  createDate: Date;

  @UpdateDateColumn()
  updateDate: Date;
}
```

### Controller Code: `MetaOptionsController`
```ts
import { Controller } from '@nestjs/common';

@Controller('meta-options')
export class MetaOptionsController {}
```

### Module Code: `MetaOptionsModule`
```ts
import { Module } from '@nestjs/common';
import { MetaOptionsController } from './meta-options.controller';

@Module({
  controllers: [MetaOptionsController],
})
export class MetaOptionsModule {}
```

### Integration Snippet in AppModule
```ts
import { MetaOptionsModule } from './meta-options/meta-options.module';

@Module({
  imports: [MetaOptionsModule, ...],
})
export class AppModule {}
```

### Commit Ref: [Autoloading Entities](https://github.com/NadirBakhsh/nestjs-resources-code/commit/7d0dbfdb4d3dd94893ea0b3fba371fec9209f240)

---

## One to One Relationships

A **1-to-1 (one-to-one)** relationship occurs when **one record in a table is linked to only one record in another table** — and vice versa.

> **Example**: One `Post` has one `MetaOption`, and one `MetaOption` is linked to one `Post`.

### 🔗 How is this Relationship Implemented?
- In a 1-to-1 relationship, **one table’s primary key is used as a foreign key in another table**.
- This foreign key acts as the **link between the two tables**.

#### 📊 Example Schema:

#### `Post` Table
| post_id (PK) | title         | meta_option_id (FK) |
|--------------|---------------|---------------------|
| 1            | Hello World   | 1001                |

#### `MetaOption` Table
| meta_option_id (PK) | seo_title        | canonical_url         |
|---------------------|------------------|------------------------|
| 1001                | Welcome Post     | /welcome-post          |

- `meta_option_id` in `Post` is a **foreign key**, referencing the `meta_option_id` **primary key** in `MetaOption`.


### 📌 Why Use 1-to-1 Relationship?
- When you want to **separate additional information** about an entity (like post metadata) but **keep it tied closely** to the original entity.
- Helps in **data normalization**, improving data organization and clarity.

### 🔄 Direction of Relationships

#### ▶️ Unidirectional:
- Only one side knows about the relationship.
- **Post → MetaOption** (Post knows its MetaOption, but MetaOption doesn’t directly reference Post).

#### 🔁 Bidirectional:
- Both tables reference each other.
- **Post ↔ MetaOption** (Each knows the other — e.g., MetaOption may include a `post_id` back-reference).



### 📌 Summary
| Concept                     | Description                                      |
|----------------------------|--------------------------------------------------|
| Relationship Type          | One-to-One                                       |
| Foreign Key Location       | `meta_option_id` in `Post` references `MetaOption` |
| Cardinality                | One Post → One MetaOption                        |
| Directionality             | Can be Unidirectional or Bidirectional           |

---
## Uni-Directional One to One Relationship

![alt text](./images/uni-directional.png)


A **Unidirectional One-to-One relationship** in TypeORM is when only one of the entities is aware of the relationship. This means the relationship is defined only in one entity class, and the other entity does not have any reference back to the first entity.


In a **Unidirectional One-to-One**, we use two decorators:
- `@OneToOne(() => TargetEntity)`
- `@JoinColumn()`

`@JoinColumn()` specifies which side owns the relationship and adds the foreign key column in that table.


### GitHub Code Example

#### `post.entity.ts`
```ts
import {
  Column,
  Entity,
  JoinColumn,
  OneToOne,
  PrimaryGeneratedColumn,
} from 'typeorm';
import { postType } from './enums/postType.enum';
import { postStatus } from './enums/postStatus.enum';
import { MetaOption } from 'src/meta-options/meta-option.entity';

@Entity()
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @Column()
  content: string;

  @OneToOne(() => MetaOption)
  @JoinColumn() // foreign key created in Post table
  metaOptions?: MetaOption;

  // additional fields
  @Column({ nullable: true })
  publishOn?: Date;

  @Column('simple-array')
  tags: string[];
}
```

#### `meta-option.entity.ts`
```ts
import { Entity, Column, PrimaryGeneratedColumn } from 'typeorm';

@Entity()
export class MetaOption {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  seoTitle: string;

  @Column()
  seoDescription: string;
}
```


#### Schema Overview
```
Post Table:
- id (PK)
- title
- content
- metaOptionsId (FK -> MetaOption.id)
- publishOn
- tags

MetaOption Table:
- id (PK)
- seoTitle
- seoDescription
```


## Key Takeaways
- `@JoinColumn()` used only on one side (Post).
- Foreign Key column created in Post table.
- MetaOption entity has no reference to Post (Unidirectional).



## Code Reference:
GitHub Commit Example: [nestjs-resources-code@6ebe58f](https://github.com/NadirBakhsh/nestjs-resources-code/commit/6ebe58feef72d3d9cea52509d91f6e6458045b41)

---

## Creating MetaOptions Service
![alt text](./images/metaOptions.png)

1. meta-options.post.endpoints.http
- 📌 Adds a sample HTTP request for testing the POST /meta-options endpoint.

```http
POST http://localhost:3000/meta-options
Content-Type: application/json

{
  "metaValue": "{\"sidebarEnabled\": true, \"footerActive\": true}"
}
```

2. meta-options.controller.ts

- Imports Body, Post, and DTO.
- Injects MetaOptionsService.
- Defines @Post() endpoint.

```ts
@Post()
public create(@Body() createPostMetaOptionsDto: CreatePostMetaOptionsDto) {
  return this.metaOptionsService.create(createPostMetaOptionsDto);
}
```

3. meta-options.module.ts

- Adds MetaOptionsService as a provider.
- Ensures TypeOrmModule is configured for the MetaOption entity.

```ts
@Module({
  controllers: [MetaOptionsController],
  imports: [TypeOrmModule.forFeature([MetaOption])],
  providers: [MetaOptionsService],
})
export class MetaOptionsModule {}
```

4. providers/meta-options.service.ts

- Implements MetaOptionsService with a create() method.
- Uses TypeORM Repository to persist MetaOption entities.

```ts
@Injectable()
export class MetaOptionsService {
  constructor(
    @InjectRepository(MetaOption)
    private readonly metaOptionRepository: Repository<MetaOption>,
  ) {}

  public async create(createPostMetaOptionsDto: CreatePostMetaOptionsDto) {
    const metaOption = this.metaOptionRepository.create(createPostMetaOptionsDto);
    return await this.metaOptionRepository.save(metaOption);
  }
}
```

code reference: [GitHub Commit Example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/2c095a4f1e736d80b2b769ab3244c68c580aae8c)

---
## Creating Post with Relationships

![alt text](./images/create-relationships.png)

### 📦 Changes Overview

#### 1. **DTO Modification**
- Updated `CreatePostDto`:
  - Replaced `metaOptions` array of objects with a single `metavalue` JSON object.
  - Type changed to `object`, documented as a JSON string.
  - Simplified the schema example.

#### 2. **Controller Update**
- `PostsController.createPost()`:
  - Removed debug `console.log`.
  - Delegates logic to `PostsService.create()`.

#### 3. **Service Logic Enhancement**
- `PostsService.create()`:
  - Handles both `Post` and `MetaOption` creation.
  - Saves meta options (if provided), then attaches them to the new post.
  - Returns the saved post with relationships.

#### 4. **Entity Injection**
- Injected `MetaOption` repository using `@InjectRepository`.
- Updated `PostsModule` to include `MetaOption` in `TypeOrmModule.forFeature`.

---

### ⚙️ Technical Benefits

- Enables relational data handling (`Post` + `MetaOption`) in a single API call.
- Clean and modular NestJS service structure.
- Prepares DTO for flexible `metaOptions` usage via JSON.

---

### 📎 Related Files Modified

- `createPost.dto.ts`
- `posts.controller.ts`
- `posts.service.ts`
- `posts.module.ts`
- Sample payload updated in comments

### 🔗 [View Commit on GitHub](https://github.com/NadirBakhsh/nestjs-resources-code/commit/ac9260f8f674fd2141182a30e23f1b5828d8cbf3)


---
## Cascade Creation with Relationships

### What is Cascade in TypeORM?

**Cascade** is a feature in TypeORM (and relational databases) that allows related entities to be automatically persisted, updated, or removed when you perform these actions on the parent entity. This is especially useful for relationships like One-to-One or One-to-Many, where you want to create or update related records in a single operation.

### Why Use Cascade?

- **Simplifies code:** You don't need to manually save related entities before saving the parent.
- **Reduces dependencies:** No need to inject or use multiple repositories for related entities.
- **Keeps logic clean:** All related data is handled together, making your service methods shorter and easier to test.

### How to Enable Cascade

To enable cascade, add the `cascade` option to your relationship decorator (e.g., `@OneToOne`, `@OneToMany`).  
You can set `cascade: true` for all operations, or specify an array of actions (`['insert', 'update', 'remove', 'soft-remove', 'recover']`).

**Example:**

```ts
@OneToOne(() => MetaOption, { cascade: true })
@JoinColumn()
metaOptions?: MetaOption;
```

Or for specific actions:

```ts
@OneToOne(() => MetaOption, { cascade: ['insert', 'remove'] })
@JoinColumn()
metaOptions?: MetaOption;
```

### How It Works

With cascade enabled, you can include the related entity directly in your DTO or object graph when saving:

```ts
// In your service:
const post = this.postRepository.create({
  title: 'My Post',
  content: '...',
  metaOptions: {
    metaValue: '{"sidebarEnabled": true, "footerActive": true}',
  },
});
await this.postRepository.save(post);
// Both Post and MetaOption are created in one operation!
```

### Benefits

- **Single save operation**: Both the main entity and its related entities are created/updated together.
- **No manual repository calls**: No need to separately create and link related entities.
- **Cleaner tests and code**: Fewer dependencies and less boilerplate.

### Key Points

- Cascade can be enabled for all or specific actions.
- Use cascade to simplify creation, update, and deletion of related entities.
- Especially useful for tightly-coupled relationships (e.g., Post and MetaOption).

Code reference: [GitHub Commit Example](https://github.com/NadirBakhsh/nestjs-resources-code/commit/ee3bbbf84b024b5481e9b3633eae6f316095f696))

---

## Querying with Eager Loading
---
## Deleting Related Entities
---
## Bi-Directional One to One Relationship
---
## Creating a Bi-Directional Relationship
---
## Cascade Delete with Bi-Directional Relationship
---
## One to Many Relationships
---
## Creating One to Many Relationship
---
## Create Post with Author
---
## Eager Loading Author
---
## Many to Many Relationships
---
## Practice: Service to Create Tags
---
## Solution: TagsService
---
## Testing Tags Service
---
## Uni-Directional Many to Many Relationship
---
## Querying Many to Many Relationship
---
## Updating Post with New Tags
---
## Deleting Post and Relationship
---
## Bi-Directional Many to Many Relationship
---
## Cascade Delete with Many to Many
---
## Soft Delete Tags
---