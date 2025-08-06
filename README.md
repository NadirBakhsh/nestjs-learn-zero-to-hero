# Step 10 - Transactions Using TypeORM

### Understanding Transactions
![understanding-transactions](./images/understanding-transactions.png)


![example-of-related-operations](./images/example-of-related-operations.png)

1. **What is a Transaction?**

- A transaction is a complete set of CRUD operations on a database that are interrelated.
- If any operation within a transaction fails, the entire transaction is rolled back to prevent data inconsistency.

2. Why Use Transactions?

![why-use-transactions](./images/why-use-transactions.png)

- Ensures data integrity: All operations succeed or none do.
- Prevents partial updates that could lead to data discrepancies.
- Provides rollback capability if any step fails. 

3. Example Scenario: Money Transfer
- Steps: Check balance → Deduct from sender → Credit receiver.
- If any step fails, all changes are reverted.

4. Application Example
- Creating multiple users in one request.
- If creating any user fails, all previously created users in that transaction are rolled back.

5. Advantages of Transactions
- Data assurance: Only successful if all operations succeed.
- Automatic rollback on failure.
- Useful for multiple, interrelated CRUD operations across one or more entities.

6. TypeORM Support
- TypeORM provides tools (like QueryRunner) to manage transactions in your NestJS application.

Summary:
Use transactions when you need to perform multiple related database operations that must all succeed together. Transactions help maintain data integrity and consistency by ensuring that either all operations are applied or none are.

---

### TypeORM QueryRunner

**QueryRunner** is a TypeORM class that allows you to manage a single database connection for advanced operations like transactions. Instead of using the default connection pool, QueryRunner gives you one dedicated connection for the entire transaction, ensuring all related operations are executed together.

![query-runner-class](./images/query-runner-class.png)

#### How to Use QueryRunner for Transactions

![pp-transaction](./images/pp-transaction.png)

1. **Create a QueryRunner instance:**  
   Use `dataSource.createQueryRunner()` to get a new QueryRunner.

2. **Connect to the database:**  
   Call `queryRunner.connect()`.

3. **Start the transaction:**  
   Use `queryRunner.startTransaction()`.

4. **Perform your CRUD operations:**  
   Use `queryRunner.manager` to run your queries.

5. **Commit or rollback:**  
   - If all operations succeed, call `queryRunner.commitTransaction()`.
   - If any operation fails, call `queryRunner.rollbackTransaction()`.

6. **Release the connection:**  
   Always call `queryRunner.release()` in a `finally` block to free the connection.

#### Example

```typescript
const queryRunner = dataSource.createQueryRunner();

await queryRunner.connect();
await queryRunner.startTransaction();

try {
  // Perform related DB operations
  await queryRunner.manager.save(...);
  await queryRunner.manager.update(...);
  await queryRunner.manager.delete(...);

  await queryRunner.commitTransaction(); // Success!
} catch (err) {
  await queryRunner.rollbackTransaction(); // Undo all changes
} finally {
  await queryRunner.release(); // Always release connection
}
```

**Summary:**  
Use QueryRunner when you need full control over transactions in TypeORM. It ensures all your related operations either succeed together or fail together, maintaining

---

### Creating First Transaction




- Why Create Post is Not a Transaction
- Creating Multiple Providers
- Updating the DTO
- Practice: Handle Exceptions for CreateManyUsers
- Solution: Handle Exceptions for CreateManyUsers