![image](https://github.com/user-attachments/assets/9ba1b01c-1de5-497f-b353-ef6fb8408f33)


### 1. **Atomicity**:
   - **Definition**: Ensures that a transaction is **all or nothing**. It means that either all operations in a transaction are successfully completed, or if any operation fails, the entire transaction is **rolled back** to its initial state.
   - **Example**: In a bank transfer, if money is being transferred from Account A to Account B, atomicity ensures that if part of the operation fails (e.g., deducting money from Account A but failing to deposit in Account B), both actions are undone, and no partial transfer happens.

### 2. **Consistency**:
   - **Definition**: Guarantees that a transaction brings the database from one **valid state to another**. It ensures that the database follows all defined rules, constraints, and data integrity (like foreign keys, unique constraints, etc.) during the transaction.
   - **Example**: If a transaction violates a constraint (like inserting a negative value for the balance in a bank account), it will be **rejected**, ensuring that the database remains in a consistent state.

### 3. **Isolation**:
   - **Definition**: Ensures that **transactions are isolated from each other**. The operations of one transaction are not visible to other transactions until it is fully completed. This prevents issues like dirty reads, non-repeatable reads, and phantom reads in concurrent transactions.
   - **Example**: In a system where multiple transactions are happening at once (e.g., multiple users accessing a bank's database), isolation ensures that one user's transaction does not interfere with another's, and each sees a consistent state of the database.

### 4. **Durability**:
   - **Definition**: Once a transaction is **committed**, its changes are **permanent** and will survive any subsequent system failures, like a power loss or database crash.
   - **Example**: After a successful transfer of funds between bank accounts, the system will ensure that even if the database crashes right after the transaction is committed, the changes will be saved, and the transaction won't be lost.

---

### Summary of ACID:
- **Atomicity**: "All or nothing" – the transaction is completed fully or not at all.
- **Consistency**: The transaction must leave the database in a valid state.
- **Isolation**: Transactions are executed as though they are the only ones interacting with the database.
- **Durability**: Once a transaction is committed, it is permanent, even in the event of a failure.

