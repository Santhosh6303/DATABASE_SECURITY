# MySQL Authentication and Authorization Guide

## Example Database: Online Bookstore

Let's assume we have a database called `bookstore` with tables like `books`, `users`, and `orders`.

```sql
CREATE DATABASE bookstore;
USE bookstore;

CREATE TABLE books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(100),
    author VARCHAR(100),
    price DECIMAL(10, 2)
);

CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50),
    email VARCHAR(100)
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    book_id INT,
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id)
);
```

## Authentication

Authentication in MySQL verifies the identity of users connecting to the database.

### 1. Creating Users

```sql
-- Create a user with default authentication plugin
CREATE USER 'bookstore_admin'@'localhost' IDENTIFIED BY 'strong_password';

-- Create a user with specific authentication plugin
CREATE USER 'bookstore_reader'@'localhost' IDENTIFIED WITH mysql_native_password BY 'reader_password';

-- Create a user with advanced security options
CREATE USER 'santhosh_Cheemala'@'localhost'
    IDENTIFIED WITH caching_sha2_password BY 'Password2024!'
    PASSWORD EXPIRE INTERVAL 180 DAY
    FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;

-- Explanation of advanced options:
-- - Uses caching_sha2_password for authentication
-- - Password expires every 180 days
-- - Account locks for 2 days after 3 failed login attempts
```

### 2. Changing Authentication Plugin

MySQL supports various authentication plugins. Common ones include:
- `mysql_native_password` (default in older versions)
- `caching_sha2_password` (default in MySQL 8.0+)
- `sha256_password`

To change a user's authentication plugin:

```sql
-- Change to caching_sha2_password
ALTER USER 'bookstore_reader'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'new_password';

-- Change to sha256_password
ALTER USER 'bookstore_admin'@'localhost' IDENTIFIED WITH sha256_password BY 'new_admin_password';
```

### 3. Viewing User Authentication

```sql
SELECT user, host, plugin, password_expired, password_lifetime, account_locked
FROM mysql.user WHERE user LIKE 'bookstore%' OR user = 'shravasti_ohol';
```

### 4. Additional Security Features

```sql
-- Set password strength policy
SET GLOBAL validate_password.policy = STRONG;
SET GLOBAL validate_password.length = 12;

-- Require SSL for user connections
ALTER USER 'bookstore_admin'@'localhost' REQUIRE SSL;

-- Set account to expire on a specific date
ALTER USER 'seasonal_user'@'localhost' PASSWORD EXPIRE AT '2024-12-31';

-- Prevent user from changing their own password
ALTER USER 'restricted_user'@'localhost' PASSWORD EXPIRE NEVER;
```

## Authorization

Authorization determines what actions authenticated users can perform.

### 1. Granting Privileges

```sql
-- Grant all privileges on bookstore database to admin
GRANT ALL PRIVILEGES ON bookstore.* TO 'bookstore_admin'@'localhost';

-- Grant read-only access to reader
GRANT SELECT ON bookstore.* TO 'bookstore_reader'@'localhost';

-- Grant read and insert privileges on specific tables
GRANT SELECT, INSERT ON bookstore.books TO 'bookstore_manager'@'localhost';
GRANT SELECT, INSERT ON bookstore.orders TO 'bookstore_manager'@'localhost';

-- Apply the changes
FLUSH PRIVILEGES;
```

### 2. Viewing Granted Privileges

```sql
SHOW GRANTS FOR 'bookstore_reader'@'localhost';
SHOW GRANTS FOR 'bookstore_admin'@'localhost';
```

### 3. Revoking Privileges

```sql
-- Revoke insert privilege from manager on books table
REVOKE INSERT ON bookstore.books FROM 'bookstore_manager'@'localhost';
```

### 4. Creating Role-Based Access

```sql
-- Create roles
CREATE ROLE 'book_editor', 'order_processor';

-- Grant privileges to roles
GRANT SELECT, INSERT, UPDATE, DELETE ON bookstore.books TO 'book_editor';
GRANT SELECT, INSERT, UPDATE ON bookstore.orders TO 'order_processor';

-- Assign roles to users
GRANT 'book_editor' TO 'bookstore_manager'@'localhost';
GRANT 'order_processor' TO 'bookstore_manager'@'localhost';

-- User needs to activate the roles in their session
SET ROLE 'book_editor', 'order_processor';
```
