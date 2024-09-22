# Comprehensive SQL Quick Reference Guide

## Table Definitions
```sql
College (cName: varchar(10), state: varchar(10), enrollment: int)
Student (sID: int, sName: varchar(10), CGPA: float(2,1), sizeHS: int)
Apply (sID: int, cName: varchar(10), major: varchar(20))
```

## Basic SQL Queries

### SELECT
- Retrieve data from a table
```sql
SELECT * FROM Student;
SELECT sName, CGPA FROM Student WHERE sizeHS > 1000;
```

### INSERT
- Add new rows to a table
```sql
INSERT INTO Student (sID, sName, CGPA, sizeHS) VALUES (1, 'John', 3.5, 1200);
```

### UPDATE
- Modify existing data in a table
```sql
UPDATE Student SET CGPA = 3.8 WHERE sID = 1;
```

### DELETE
- Remove rows from a table
```sql
DELETE FROM Student WHERE CGPA < 2.0;
```

## Joining Tables

### INNER JOIN
- Returns matching rows from both tables
```sql
SELECT Student.sName, Apply.cName, Apply.major
FROM Student INNER JOIN Apply ON Student.sID = Apply.sID;
```

### LEFT JOIN
- Returns all rows from the left table and matching rows from the right table
```sql
SELECT Student.sName, Apply.cName
FROM Student LEFT JOIN Apply ON Student.sID = Apply.sID;
```

### RIGHT JOIN
- Returns all rows from the right table and matching rows from the left table
```sql
SELECT Apply.cName, College.state
FROM Apply RIGHT JOIN College ON Apply.cName = College.cName;
```

### FULL OUTER JOIN
- Returns all rows when there's a match in either table
```sql
SELECT Student.sName, Apply.cName
FROM Student FULL OUTER JOIN Apply ON Student.sID = Apply.sID;
```

## Aggregate Functions and Grouping

### GROUP BY
- Group rows that have the same values
```sql
SELECT cName, COUNT(*) as applicants
FROM Apply
GROUP BY cName;
```

### HAVING
- Specify a search condition for a group
```sql
SELECT cName, COUNT(*) as applicants
FROM Apply
GROUP BY cName
HAVING COUNT(*) > 100;
```

## Subqueries
- Nested queries within a main query
```sql
SELECT sName
FROM Student
WHERE sID IN (SELECT sID FROM Apply WHERE major = 'CS');
```

## Altering Tables

### ADD COLUMN
```sql
ALTER TABLE Student ADD COLUMN age INT;
```

### DROP COLUMN
```sql
ALTER TABLE Student DROP COLUMN age;
```

### MODIFY COLUMN
```sql
ALTER TABLE Student MODIFY COLUMN sName VARCHAR(20);
```

## Advanced Queries

### UNION
- Combine result sets of two or more SELECT statements
```sql
SELECT cName FROM College
UNION
SELECT cName FROM Apply;
```

### EXISTS
- Check for the existence of rows that satisfy a subquery
```sql
SELECT sName
FROM Student
WHERE EXISTS (SELECT * FROM Apply WHERE Apply.sID = Student.sID);
```

### LIKE
- Pattern matching in strings
```sql
SELECT * FROM College WHERE state LIKE 'C%';
```

## Table Constraints and Modifications

### Primary Keys
```sql
ALTER TABLE College ADD CONSTRAINT pk_college PRIMARY KEY (cName);
ALTER TABLE Student ADD CONSTRAINT pk_student PRIMARY KEY (sID);
ALTER TABLE Apply ADD CONSTRAINT pk_apply PRIMARY KEY (sID, cName, major);
```

### Foreign Keys
```sql
ALTER TABLE Apply ADD CONSTRAINT fk_apply_student 
    FOREIGN KEY (sID) REFERENCES Student(sID);
ALTER TABLE Apply ADD CONSTRAINT fk_apply_college 
    FOREIGN KEY (cName) REFERENCES College(cName);
```

### Modify Column
```sql
ALTER TABLE Apply MODIFY major VARCHAR(25);
```

### Add Column with Constraint
```sql
ALTER TABLE Apply ADD decision VARCHAR2(3) NOT NULL;
```

### Change Column Data Type
```sql
ALTER TABLE Apply MODIFY decision CHAR(1);
```

### Drop Constraints
```sql
ALTER TABLE Apply DROP CONSTRAINT fk_apply_college;
```

### Drop Column
```sql
ALTER TABLE Student DROP COLUMN sizeHS;
```

### Drop Primary Key
```sql
ALTER TABLE College DROP PRIMARY KEY;
```

### Add Foreign Key with ON DELETE CASCADE
```sql
ALTER TABLE Apply ADD CONSTRAINT fk_apply_college 
    FOREIGN KEY (cName) REFERENCES College(cName) ON DELETE CASCADE;
```

### Modify Foreign Key to ON DELETE SET NULL
```sql
ALTER TABLE Apply DROP CONSTRAINT fk_apply_student;
ALTER TABLE Apply ADD CONSTRAINT fk_apply_student 
    FOREIGN KEY (sID) REFERENCES Student(sID) ON DELETE SET NULL;
```

### Rename Column
```sql
ALTER TABLE College RENAME COLUMN enrollment TO enroll;
```

## Complex Queries

### Counting and Aggregation
```sql
-- Count total number of Students
SELECT COUNT(*) FROM Student;

-- Calculate average GPA
SELECT AVG(CGPA) FROM Student;

-- Find min and max GPA
SELECT MAX(CGPA) AS max_GPA, MIN(CGPA) AS min_GPA FROM Student;

-- Count students with GPA >= 3.7
SELECT COUNT(*) FROM Student WHERE CGPA >= 3.7;

-- Multiple aggregations
SELECT MAX(CGPA), AVG(CGPA), MIN(CGPA), SUM(CGPA) FROM Student;

-- Count distinct values
SELECT COUNT(DISTINCT major) FROM Apply;

-- Aggregate with conditions
SELECT COUNT(*) FROM Apply WHERE decision = 'Y';
```

### Grouping and Having
```sql
-- Group by major and count applications
SELECT major, COUNT(sID) AS No_of_applications
FROM Apply
GROUP BY major;

-- Applications per college with condition
SELECT cName, COUNT(*) AS app_count
FROM Apply
GROUP BY cName
HAVING COUNT(*) >= 2;

-- Colleges with applications from 3 or more students
SELECT cName, COUNT(DISTINCT sID) AS app_count
FROM Apply
GROUP BY cName
HAVING COUNT(DISTINCT sID) >= 3;
```

### Joins and Subqueries
```sql
-- Left join to include all students
SELECT s.sName, COUNT(a.sID) AS app_count
FROM Student s
LEFT JOIN Apply a ON s.sID = a.sID
GROUP BY s.sName;

-- Students with 3 or more applications
SELECT s.sName
FROM Student s
JOIN (
    SELECT sID
    FROM Apply
    GROUP BY sID
    HAVING COUNT(*) >= 3
) a ON s.sID = a.sID;

-- Students who haven't applied anywhere
SELECT sName
FROM Student
WHERE sID NOT IN (SELECT DISTINCT sID FROM Apply);
```

### Complex Aggregations
```sql
-- Max, Avg, Min GPA of applicants per college
SELECT a.cName, 
       MAX(s.CGPA) AS max_GPA, 
       AVG(s.CGPA) AS avg_GPA, 
       MIN(s.CGPA) AS min_GPA
FROM Apply a
JOIN Student s ON a.sID = s.sID
GROUP BY a.cName;

-- GPA frequency
SELECT CGPA, COUNT(*) AS frequency
FROM Student
GROUP BY CGPA;

-- Students with names starting with A, B, or C
SELECT COUNT(*) 
FROM Student 
WHERE sName LIKE 'A%' OR sName LIKE 'B%' OR sName LIKE 'C%';
```

Remember to adapt these queries to your specific database schema and requirements. Practice these queries to solidify your understanding before the exam.
