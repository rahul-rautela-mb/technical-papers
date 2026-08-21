# Important Database Concepts

## 1. ACID

ACID properties are used to make database transactions reliable.

ACID stands for:

- Atomicity
- Consistency
- Isolation
- Durability

A transaction is a group of database operations that should be treated as one unit.

For example, during a bank transfer, money should not be deducted from one account without being added to the other.

### 1.1 Atomicity

Atomicity means that a transaction should either complete completely or not happen at all.

For example, during a bank transfer, money should not be deducted from one account without being added to the other.

If the second operation fails, we can use `ROLLBACK`. The first operation will also be undone.

So, simply:

> Atomicity = All or Nothing

### 1.2 Consistency

Consistency means that a transaction should keep the database in a valid state. Database rules such as primary keys, foreign keys, and constraints help maintain consistency.

For example, if an account is not allowed to have a negative balance, a `CHECK` constraint can be used to enforce this rule.

So:

> Consistency = Database rules should remain valid

### 1.3 Isolation

Isolation is related to multiple transactions running at the same time.

For example, if two users try to update the same account simultaneously, their operations should not interfere with each other in an incorrect way.

Database isolation levels control how much one transaction can see from another transaction.

So:

> Isolation = Transactions should be properly separated

### 1.4 Durability

Durability means that once a transaction is committed, its changes should remain saved.

After `COMMIT`, the database should not simply lose the change if the database server restarts.

So:

> Durability = Committed data stays saved

---

## 2. CAP Theorem

CAP theorem is mainly related to distributed databases.

CAP stands for:

- Consistency
- Availability
- Partition Tolerance

Consistency means every user should get the correct/latest data according to the system's consistency guarantees.

Availability means the system should continue responding even if some servers fail.

Partition Tolerance means the system should continue working even when communication between database servers is interrupted.

When a network partition happens, a distributed system generally has to choose between stronger consistency and stronger availability.

- **CP** → Consistency + Partition Tolerance
- **AP** → Availability + Partition Tolerance

---

## 3. Joins

Joins are used to combine data from two or more tables.

Suppose we have employees with `employee_id`, `name`, and `department_id`, and departments with `department_id` and `department_name`.

Example:

```sql
SELECT e.name, d.department_name
FROM employees e
JOIN departments d
ON e.department_id = d.department_id;
```

Some common joins are:

- **INNER JOIN** – returns matching records.
- **LEFT JOIN** – returns all records from the left table and matching records from the right.
- **RIGHT JOIN** – returns all records from the right table.
- **FULL JOIN** – returns matching and unmatched records from both tables.
- **CROSS JOIN** – produces every possible combination.
- **SELF JOIN** – joins a table with itself.

---

## 4. Aggregations and Filters

### Filters

Filters are used to get only the rows we need.

```sql
SELECT *
FROM employees
WHERE salary > 50000;
```

Common filtering operators include:

```text
=, >, <, >=, <=, IN, BETWEEN, LIKE, IS NULL
```

We can also combine conditions using `AND` and `OR`.

### Aggregations

Aggregation is used to calculate values from multiple rows.

Common functions are:

```text
COUNT()
SUM()
AVG()
MIN()
MAX()
```

Example:

```sql
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id;
```

`GROUP BY` creates groups, while `HAVING` is used to filter those groups.

A simple way to remember it:

```text
WHERE  → filters rows
HAVING → filters groups
```

---

## 5. Normalization

Normalization is a way of organizing tables to reduce duplicate data.

For example, instead of storing customer information repeatedly in every order, we can separate Customers and Orders tables and connect them using `customer_id`.

Some common normal forms are:

- **1NF** – values should be atomic.
- **2NF** – removes partial dependencies.
- **3NF** – removes unnecessary transitive dependencies.

Normalization helps reduce:

- Duplicate data
- Update problems
- Insert problems
- Delete problems

However, too much normalization can result in many joins, so sometimes databases use **denormalization** for performance.

---

## 6. Indexes

An index helps the database find data faster.

For example:

```sql
CREATE INDEX idx_employee_name
ON employees(name);
```

Now a query such as:

```sql
SELECT *
FROM employees
WHERE name = 'Rahul';
```

may be faster on a large table.

Indexes are useful for columns that are frequently used for searching, filtering, joining, and sorting.

But indexes also have a disadvantage. They take extra storage and need to be updated when data changes.

So we should not create indexes on every column without considering the actual queries.

---

## 7. Transactions

A transaction is a group of database operations treated as one unit.

The basic commands are:

```sql
BEGIN;
COMMIT;
ROLLBACK;
```

For example:

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 1000
WHERE account_id = 1;

UPDATE accounts
SET balance = balance + 1000
WHERE account_id = 2;

COMMIT;
```

If something goes wrong, we can use `ROLLBACK`.

Transactions are especially important for operations such as:

- Bank transfers
- Orders
- Payments
- Inventory updates

---

## 8. Locking Mechanism

Locking is used when multiple transactions are trying to access the same data.

For example, two users should not be able to reduce the same product's stock incorrectly at the same time.

A row can be locked for update using:

```sql
SELECT *
FROM products
WHERE product_id = 10
FOR UPDATE;
```

Commonly discussed locks include:

- **Shared Lock** – mainly used when reading.
- **Exclusive Lock** – used when modifying data.

Locks help prevent incorrect concurrent updates.

However, locks can also cause deadlocks.

A deadlock happens when:

```text
Transaction A → waiting for B
Transaction B → waiting for A
```

The database normally detects this and cancels one of the transactions.

---

## 9. Database Isolation Levels

Isolation levels control how transactions see changes made by other transactions.

The commonly used levels are:

1. Read Uncommitted
2. Read Committed
3. Repeatable Read
4. Serializable

### Dirty Read

A transaction reads data that another transaction has changed but not committed.

### Non-Repeatable Read

A transaction reads the same row twice and gets different values because another transaction changed it.

### Phantom Read

A query returns a different set of rows when executed again because another transaction inserted or deleted matching rows.

### Simple comparison

| Isolation Level | Dirty Read | Non-Repeatable Read | Phantom Read |
|---|---|---|---|
| Read Uncommitted | Possible | Possible | Possible |
| Read Committed | Prevented | Possible | Possible |
| Repeatable Read | Prevented | Prevented | DB dependent |
| Serializable | Prevented | Prevented | Prevented |

Higher isolation generally gives stronger guarantees but can reduce concurrency.

---

## 10. Triggers

A trigger is database logic that automatically runs when a particular event occurs.

Common events are:

```text
INSERT
UPDATE
DELETE
```

For example, we may want to record whenever an employee is updated.

A trigger can automatically insert information into an audit table.

In PostgreSQL, a trigger can be created using:

```sql
CREATE TRIGGER employee_update_trigger
AFTER UPDATE
ON employees
FOR EACH ROW
EXECUTE FUNCTION log_employee_change();
```

Triggers are useful for:

- Audit logs
- Automatically updating information
- Maintaining certain database rules

But too many triggers can make the application harder to understand because some operations happen automatically in the background.

---

# References

1. W3Schools – SQL Tutorial  
   https://www.w3schools.com/sql/

2. PostgreSQL Documentation – SQL and Database Concepts  
   https://www.postgresql.org/docs/current/

3. YouTube – SQL Course for Beginners by Programming with Mosh  
   https://www.youtube.com/watch?v=7S_tz1z_5bA

