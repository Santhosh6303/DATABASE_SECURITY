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

#### Example 4: Using a Cursor in a Stored Procedure
```sql
DELIMITER //

CREATE PROCEDURE UpdateStudentGrades()
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE student_id INT;
    DECLARE total_marks DECIMAL(5,2);
    DECLARE grade CHAR(1);
    
    -- Declare cursor for fetching student data
    DECLARE student_cursor CURSOR FOR 
        SELECT id, total_marks FROM Student;
    
    -- Declare handler for when no more rows to fetch
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    
    -- Open the cursor
    OPEN student_cursor;
    
    -- Start the loop
    read_loop: LOOP
        -- Fetch the next row
        FETCH student_cursor INTO student_id, total_marks;
        
        -- Exit loop if no more rows
        IF done THEN
            LEAVE read_loop;
        END IF;
        
        -- Determine grade based on total marks
        SET grade = CASE
            WHEN total_marks >= 90 THEN 'A'
            WHEN total_marks >= 80 THEN 'B'
            WHEN total_marks >= 70 THEN 'C'
            WHEN total_marks >= 60 THEN 'D'
            ELSE 'F'
        END;
        
        -- Update the student's grade
        UPDATE Student
        SET grade = grade
        WHERE id = student_id;
        
    END LOOP;
    
    -- Close the cursor
    CLOSE student_cursor;
END //

DELIMITER ;
```

This example demonstrates how to use a cursor in a stored procedure:

1. We declare a cursor that selects student IDs and total marks from the Student table.
2. We use a loop to fetch each row from the cursor.
3. For each student, we calculate a grade based on their total marks.
4. We then update the student's grade in the database.
5. The process continues until all rows have been processed.

Cursors are useful when you need to perform operations on each row in a result set individually. However, they can be less efficient than set-based operations for large datasets, so use them judiciously.

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
