# SQL Stored Procedures and Triggers: Quick Reference

## Table of Contents
1. [Stored Procedures](#stored-procedures)
   - [Definition](#definition)
   - [Basic Syntax](#basic-syntax)
   - [Key Points](#key-points)
   - [Examples](#examples)
2. [Triggers](#triggers)
   - [Definition](#definition-1)
   - [Basic Syntax](#basic-syntax-1)
   - [Key Points](#key-points-1)
   - [Examples](#examples-1)
3. [Practice Exercises](#practice-exercises)

## Stored Procedures

### Definition
Stored procedures are precompiled SQL statements stored in the database. They can accept parameters, perform operations, and return results.

### Basic Syntax
```sql
CREATE PROCEDURE procedure_name (parameters)
BEGIN
    -- SQL statements
END;
```

### Key Points
1. Used for complex operations involving multiple SQL statements
2. Can include control flow (IF, WHILE, etc.)
3. Can return result sets or modify data
4. Improve performance by reducing network traffic

### Examples

#### Example 1: Calculate Average and Update Eligibility
```sql
CREATE PROCEDURE UpdateEligibility()
BEGIN
    DECLARE avg_marks DECIMAL(5,2);
    
    -- Calculate average marks
    SELECT AVG(total_marks) INTO avg_marks FROM Student;
    
    -- Update eligibility
    UPDATE Student
    SET Eligible = CASE 
        WHEN total_marks > avg_marks THEN 'y'
        ELSE 'n'
    END;
END;
```

#### Example 2: Create Login ID
```sql
CREATE PROCEDURE USER_LOGINS()
BEGIN
    UPDATE USERS
    SET LOGIN_ID = CONCAT(LOWER(FIRST_NAME), '@', LOWER(INST_NAME), '.in');
END;
```

#### Example 3: Deposit Money
```sql
CREATE PROCEDURE DepositMoney(
    IN p_acc_no VARCHAR(20),
    IN p_amount DECIMAL(10,2)
)
BEGIN
    UPDATE account_details
    SET balance = balance + p_amount
    WHERE acc_no = p_acc_no;
    
    INSERT INTO deposit_logs (acc_no, dep_amount, date)
    VALUES (p_acc_no, p_amount, CURDATE());
END;
```

## Triggers

### Definition
Triggers are special stored procedures that automatically execute when specific events occur on a table.

### Basic Syntax
```sql
CREATE TRIGGER trigger_name
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON table_name
FOR EACH ROW
BEGIN
    -- SQL statements
END;
```

### Key Points
1. Can be set to execute BEFORE or AFTER an event
2. Access to OLD and NEW values in UPDATE triggers
3. Used for maintaining data integrity, auditing, and complex business rules

### Examples

#### Example 1: Audit Trigger for Contacts Table
```sql
CREATE TRIGGER after_contact_insert
AFTER INSERT ON contacts
FOR EACH ROW
BEGIN
    INSERT INTO contacts_audit (contact_id, created_by, creation_date)
    VALUES (NEW.contact_id, CONCAT(NEW.first_name, ' ', NEW.last_name), NOW());
END;
```

#### Example 2: Deletion Tracking Trigger
```sql
CREATE TRIGGER before_contact_delete
BEFORE DELETE ON contacts
FOR EACH ROW
BEGIN
    INSERT INTO contacts_deletion_log (contact_id, deletion_date)
    VALUES (OLD.contact_id, NOW());
END;
```

#### Example 3: Mini-Statement Trigger
```sql
CREATE TRIGGER before_customer_update
BEFORE UPDATE ON customer
FOR EACH ROW
BEGIN
    INSERT INTO mini_statement (account_number, old_balance, timestamp)
    VALUES (OLD.account_number, OLD.available_balance, NOW());
END;
```

## Practice Exercises

1. Create a stored procedure to transfer money between two accounts.
2. Write a trigger to prevent negative balance in a bank account.
3. Develop a stored procedure to calculate and update interest for savings accounts.

Remember to adapt these examples to your specific database system, as syntax may vary slightly between different SQL implementations.
