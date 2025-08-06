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


- TypeORM QueryRunner
- Creating First Transaction
- Why Create Post is Not a Transaction
- Creating Multiple Providers
- Updating the DTO
- Practice: Handle Exceptions for CreateManyUsers
- Solution: Handle Exceptions for CreateManyUsers