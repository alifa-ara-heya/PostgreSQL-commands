## **PostgreSQL Quick Reference**

### **1. Connecting to PostgreSQL**

- **Basic connection:**
  ```bash
  psql
  ```
- **Connect with a specific user (e.g., the user is postgres here):**
  ```bash
  psql -U postgres
  ```
- **Connect to a specific database:**
  ```bash
  psql -U postgres -d test_db; #uses the test_db with the superuser Postgres
  psql -U user1 -d postgres;
  ```
- **View connection info (gives the user details and port name):**
  ```sql
  \conninfo
  ```

---

### **2. Listing Information**

- **List all databases:**
  ```sql
  \l
  ```
- **List all users/roles:**
  ```sql
  \du
  ```
- **List all schemas (inside a DB):**
  ```sql
  \dn
  ```

---

### **3. Switching Databases and Exiting**

- **Connect to another DB while in `psql`:**
  ```sql
  \c test_db
  ```

---

- **Quit `psql`:**
  ```sql
  \q
  ```

### **4. Managing Databases**

- **Create a database:**
  ```sql
  CREATE DATABASE test_db;
  --or,
  create database test_db1; --not case sensiitive
  ```

---

### **5. Managing Users / Roles**

- **Create a user:**
  ```sql
  CREATE USER user2;
  CREATE USER user1 WITH LOGIN ENCRYPTED PASSWORD '12345';
  ```
- **Create a role:**
  ```sql
  CREATE ROLE role1 WITH LOGIN ENCRYPTED PASSWORD '12345';
  ```

---

### **6. Login as New User**

- **Access database with a user:**
  ```bash
  psql -U user1 -d postgres;
  ```

> Note: User must have permission on the database.

---

### **7. Table Operations**

- **Create a table example:**
  ```sql
  CREATE TABLE test_table (
    name VARCHAR(50) --here, name is the column name, varchar is a datatype and test_table is the name of the table
  );
  ```
- **Insert data:**
  ```sql
  INSERT INTO test_table (name)
  VALUES ('ayesha');
  ```
- **Select data (to view/read everything in a table):**
  ```sql
  SELECT * FROM test_table;
  ```

---

### **8. Granting and Revoking Permissions**

- **Switch user and connect to a database:**
  ```bash
  psql -U user4 -d postgres;
  ```
  _(switching from one user to another)_
- **Grant all privileges on a table to a user:**
  ```sql
  GRANT ALL PRIVILEGES ON TABLE test_table TO user4;
  ```
  _(postgres is a superuser, but other users are not; to allow a user to access a table, the superuser must grant permissions.)_
- **Grant select (read-only) permission on a table:**
  ```sql
  GRANT SELECT ON TABLE test_table TO user3;
  ```
  _(gives permission to view the table but not insert or delete)_
- **Revoke select permission on a table:**
  ```sql
  REVOKE SELECT ON TABLE test_table FROM user3
  ```
  _(revokes viewing permission)_
- **Grant all privileges on all tables in schema `public` to a user:**
  ```sql
  GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO user3;
  ```
  _(gives permission on all tables)_
- **Grant select on all tables in schema `public` to a role:**
  ```sql
  GRANT SELECT ON ALL TABLES IN SCHEMA public TO role1;
  ```
  _(to allow role to view/read all databases)_
- **Grant a role to a user:**
  ```sql
  GRANT role1 TO user3;
  ```
  _(grants role access to user)_

### **9. Renaming and Deleting Databases**

- **Rename a database:**
  ```sql
  ALTER DATABASE test_db RENAME TO another_db;
  ```
- **Delete (drop) a database:**
  ```sql
  DROP DATABASE another_db;
  ```

### **10. Renaming and Dropping Tables**

- **Rename a table:**
  ```sql
  ALTER TABLE person RENAME TO student;
  ```
- **Delete (drop) a table:**
  ```sql
  DROP TABLE your_table;
  ```

### **11. Table with constraints**

- **Table creation template with constraints:**
  ```sql
  CREATE TABLE table_name (
      column1 datatype constraint,
      column2 datatype constraint,
      column3 datatype constraint,
      ...
  );
  ```
- **Creating Tables with Constraints example**

  ```sql

  CREATE TABLE person (
      person_id SERIAL PRIMARY KEY,        -- SERIAL auto-increments, PRIMARY KEY enforces uniqueness and NOT NULL
      first_name VARCHAR(50) NOT NULL,     -- Limits text to 50 characters, NOT NULL ensures the value must be provided
      last_name VARCHAR(50) NOT NULL,      -- Same as above, for the last name
      is_active BOOLEAN DEFAULT true,      -- BOOLEAN type, DEFAULT sets initial value to true if not provided
      age INTEGER CHECK (age >= 0),        -- INTEGER type, CHECK ensures age must be zero or greater
      email VARCHAR(255) UNIQUE            -- UNIQUE ensures no duplicate emails
  );

  ```

- **Foreign Key Constraint**
  - Values in the foreign key column must exist in the referenced primary key column.
  - Example Tables:

| **Product** |                | **Order**    |             | **Customer**    |          |
| ----------- | -------------- | ------------ | ----------- | --------------- | -------- |
| **prod_id** | **prod_title** | **order_id** | **prod_id** | **customer_id** | **name** |
| 1           | shoe           | 1            | 1           | 1               | Alice    |
| 2           | t-shirt        | 2            | 2           | 2               | Bob      |

```sql
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE products (
    prod_id SERIAL PRIMARY KEY,
    prod_title VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES customers(customer_id),
    prod_id INTEGER REFERENCES products(prod_id)      -- foreign key to products
);
--customer_id in orders table is a foreign key that references customer_id in the customers table.

--This means you cannot insert a customer_id in orders unless it exists in the customers table.
```

- **Multiple Column Primary Key Example**

  ```sql
  CREATE TABLE person2 (
      id SERIAL,
      user_name VARCHAR(20),
      age INTEGER CHECK (age >= 18),
      PRIMARY KEY (id, user_name),
      UNIQUE (user_name, age)
  );

  ```

- **Single-Row Insert template**

  ```sql
  INSERT INTO your_table (column1, column2, column3)
  VALUES (value1, value2, value3);

  ```

- **Single-Row Insert example**
  ```sql
  INSERT INTO employees (first_name, last_name, hire_date)
  VALUES ('John', 'Doe', '2022-01-22');
  ```
- **Multi-Row Insert template**

  ```sql
  INSERT INTO your_table (column1, column2, column3)
  VALUES
      (value1_1, value2_1, value3_1),
      (value1_2, value2_2, value3_2),
      ...;

  ```

- **Multi-Row Insert Example**

  ```sql
  INSERT INTO employees (first_name, last_name, hire_date)
  VALUES
      ('Jane', 'Smith', '2022-02-20'),
      ('Bob', 'Johnson', '2022-03-25');

  ```

- **If we know the columns (not recommended)**
  ```sql
  -- Assuming table person has columns (id, name, age)
  INSERT INTO persons VALUES (1, 'John Doe', 23);
  ```

### **12. Miscellaneous**

- **Clear terminal from `psql`:**

  ```sql
  \! clear   -- Linux/macOS
  \! cls     -- Windows (if works)

  ```

---

### **13. PostgreSQL Data Types**

1. **Integer Types**

| Type       | Range                                                   | Storage | Notes                                              |
| ---------- | ------------------------------------------------------- | ------- | -------------------------------------------------- |
| `INT`      | -2,147,483,648 to 2,147,483,647                         | 4 bytes | Commonly used standard integer                     |
| `BIGINT`   | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 | 8 bytes | For very large integers                            |
| `SMALLINT` | -32,768 to 32,767                                       | 2 bytes | For small integer range                            |
| `SERIAL`   | Like `INT`                                              | 4 bytes | Auto-incrementing integer, useful for primary keys |

---

2. **Floating-Point Types**

| Type      | Storage  | Precision          | Notes                                       |
| --------- | -------- | ------------------ | ------------------------------------------- |
| `FLOAT4`  | 4 bytes  | ~6 decimal digits  | Single precision                            |
| `FLOAT8`  | 8 bytes  | ~15 decimal digits | Double precision                            |
| `NUMERIC` | Variable | User-defined       | Exact precision, for financial calculations |

---

3. **Character Types**

| Type         | Storage              | Notes                                                                                |
| ------------ | -------------------- | ------------------------------------------------------------------------------------ |
| `CHAR(n)`    | Fixed length, padded | Fixed-length character string                                                        |
| `VARCHAR(n)` | Variable length      | Variable-length character string up to `n` (recommended over `CHAR(n)` saves space). |
| `TEXT`       | Variable length      | Unlimited length text, similar to `VARCHAR` but no length limit                      |

---

4. **UUID**

- **Definition:** Universally Unique Identifier type
- **Storage:** 128-bit (16 bytes), represented as 32 hex digits with hyphens
- **Example:** `3c0ab34f-51f4-4d7b-84ee-b197af61dcb3`

---

5. **Date and Time Types**

| Type          | Storage  | Notes                         |
| ------------- | -------- | ----------------------------- |
| `DATE`        | 4 bytes  | Stores calendar date          |
| `TIME`        | 8 bytes  | Stores time of day (no date)  |
| `TIMESTAMP`   | 8 bytes  | Stores both date and time     |
| `TIMESTAMPTZ` | 8 bytes  | Timestamp with time zone      |
| `INTERVAL`    | Variable | Represents time span/duration |

---

6. **Boolean**

| Type      | Storage | Notes                       |
| --------- | ------- | --------------------------- |
| `BOOLEAN` | 1 byte  | Stores TRUE or FALSE values |

### **14. Modifying Columns in PostgreSQL**

**Select Data from Table**

```sql
SELECT * FROM person3;
```

**Add a Column**

```sql
ALTER TABLE person3
ADD COLUMN email VARCHAR(25) DEFAULT 'default@email.com' NOT NULL;
```

- Adds `email` column with a default value and `NOT NULL` constraint.

**Insert Data into Table**

```sql
INSERT INTO person3 VALUES (5, 'test2', 45, 'test@gmail.com');
```

- Inserts a new row into `person3`.

**Delete a Column**

```sql
ALTER TABLE person3 DROP COLUMN email;
```

- Removes the `email` column from `person3`.

**Rename a Column**

```sql
ALTER TABLE person3 RENAME COLUMN age TO user_age;
```

- Renames `age` column to `user_age`.

**Change Data Type of a Column**

```sql
ALTER TABLE person3 ALTER COLUMN user_name TYPE VARCHAR(50);
```

- Alters the `user_name` column to have a maximum length of 50 characters.

**Add Constraint to Existing Column**

```sql
ALTER TABLE person3 ALTER COLUMN user_age SET NOT NULL;
```

- Adds `NOT NULL` constraint to `user_age` column.

> Note: Constraints like `DEFAULT`, `UNIQUE`, `PRIMARY KEY`, and `FOREIGN KEY` cannot be added with `SET NOT NULL`.

**Delete a Constraint**

```sql
ALTER TABLE person3 ALTER COLUMN user_age DROP NOT NULL;
```

- Removes the `NOT NULL` constraint from `user_age`.

**Delete a Unique Constraint**

```sql
ALTER TABLE person3 ALTER COLUMN user_name DROP UNIQUE; --will give syntax error
```

- **Error**: You can't directly drop a `UNIQUE` constraint this way; it needs to be done via `DROP CONSTRAINT`.

**Add a New Column**

```sql
ALTER TABLE person3 ADD COLUMN user_address VARCHAR(255);
```

- Adds `user_address` column with a maximum length of 255 characters.

**Insert Data into New Column**

```sql
INSERT INTO person3 VALUES (11, 'test3', 20, 'sylhet');
```

- Inserts new data into the table.

**Add Unique Constraint**

```sql
ALTER TABLE person3
ADD CONSTRAINT unique_person3_user_address UNIQUE (user_address);
```

- Adds a `UNIQUE` constraint to the `user_address` column.

**Drop a Constraint**

```sql
ALTER TABLE person3 DROP CONSTRAINT unique_person3_user_address;
```

- Removes the `unique_person3_user_address` constraint.

### **15. PostgreSQL Operations - Queries and Commands**

---

### **16. Basic Table Operations**

- **Creating a Table named students**

```sql
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    firstName VARCHAR(50) NOT NULL,
    lastName VARCHAR(50) NOT NULL,
    age INT,
    grade CHAR(2),
    course VARCHAR(50),
    email VARCHAR(100),
    dob DATE,
    blood_group VARCHAR(5),
    country VARCHAR(50)
);
```

---

### **17. Selecting Data**

- **Select All Rows**

```sql
SELECT * FROM students;
```

- **Select Specific Column**

```sql
SELECT email FROM students;
```

- **Select or View Multiple Columns**

```sql
SELECT email, age FROM students;
```

- **Using Aliases**

```sql
SELECT email AS studentEmail FROM students;
SELECT email AS "student email" FROM students;  -- Use double quotes for column names
```

- **Order By**

```sql
SELECT * FROM students ORDER BY firstName ASC;  -- Ascending (A-Z)
SELECT * FROM students ORDER BY firstName DESC;  -- Descending (Z-A)
SELECT country FROM students ORDER BY country ASC;  -- List countries A-Z
SELECT * FROM students ORDER BY student_id;  -- sorted by ID
```

- **Distinct Values**

```sql
SELECT DISTINCT country FROM students;  -- Unique countries
```

---

### **18. Data Filtering (WHERE Clause)**

- **Filtering by Country**

```sql
SELECT * FROM students WHERE country = 'USA'; --viewing students only from the USA (conditional filtering)

```

- **Multiple Conditions (AND / OR)**

```sql
--select students with A grade in chemistry
SELECT * FROM students WHERE grade = 'A' AND course = 'Chemistry';

--select students from the USA or Canada
SELECT * FROM students WHERE country = 'USA' OR country = 'Canada';

--select students from the USA or Canada and the age is 20
SELECT * FROM students WHERE (country = 'USA' OR country = 'Canada') AND age = 20;
```

- **Filtering by Range**

```sql
SELECT * FROM students WHERE age > 20;  -- Age greater than 20
SELECT * FROM students WHERE age BETWEEN 19 AND 22;  -- Between 19 and 22

--select students older than 20 years old with course history;
SELECT * FROM students WHERE age > 20 AND course = 'History';
```

- **Not Equal**

```sql
SELECT * FROM students WHERE age <> 20;  -- Not 20 years old

--similar action but different syntax:
SELECT * FROM students WHERE age != 20;
SELECT * FROM students WHERE NOT age = 20;
```

- **String Matching (LIKE / ILIKE)**

```sql
SELECT * FROM students WHERE firstName LIKE '%am';  -- Ends with 'am'
SELECT * FROM students WHERE firstName LIKE 'A%';  -- Starts with 'A'
SELECT * FROM students WHERE firstName ILIKE 'a%';  -- Case-insensitive search
SELECT * FROM students WHERE firstName LIKE '__a';  -- Third letter is 'a'
```

### **19. Scalar Functions**

- **UPPER()**

  Converts all characters in a string to uppercase.

  ```sql
  SELECT upper(firstName) FROM students;

  --make every letter of firstName uppercase and view the whole table
  SELECT upper(firstName) AS firstNameInUppercase, * FROM students; --alias
  ```

- **LOWER()**

  Converts all characters in a string to lowercase.

  ```sql
  SELECT lower(firstName) FROM students;

  ```

- **CONCAT()**

  Concatenates two or more strings together.

  ```sql
  SELECT concat(firstName, ' ', lastName) FROM students;

  ```

- **LENGTH()**

  Returns the number of characters in a string.

  ```sql
  SELECT length(firstName) FROM students;

  ```

---

### **20. Aggregate Functions**

- **Average Age**

  AVG() Calculates the average of a set of values.

```sql
SELECT avg(age) FROM students;

```

- **Max Age**

  MAX() returns the max value in a set.

```sql
SELECT max(age) FROM students;

```

- MIN() returns the minimum value in a set.
- SUM() calculates the sum of values in a set.
- **Count Rows**

```sql
SELECT count(*) FROM students;

```

- **Find Longest Name**

```sql
SELECT max(length(firstName)) FROM students;

```

---

- **NULL Filtering**

```sql
SELECT * FROM students WHERE email IS NULL;  -- Rows with no email
SELECT * FROM students WHERE email = 'rachel.garcia@example.com';  -- Specific email

```

---

### **21. Handling NULL with COALESCE**

- **Default Placeholder for NULL (Using COALESCE for Default Values)**

```sql
SELECT COALESCE(email, 'Email Not Provided'), age, blood_group FROM students;

```

### **22. `IN` Operator**

The `IN` operator is used to check if a value matches any value in a list of values. Can be used to shorten multiple OR operators.

- **e.g. View students only from the USA, UK, and Canada:**

  ```sql
  --with OR operator
  SELECT *
  FROM students
  WHERE
      country = 'USA'
      OR country = 'Canada'
      OR country = 'UK';

  --with IN operator (shorter)
  SELECT * FROM students WHERE country IN ('USA', 'UK', 'Canada');

  ```

- **View students not from the USA, UK, and Canada:**

  ```sql
  SELECT * FROM students WHERE country NOT IN ('USA', 'UK', 'Canada');

  ```

### **23. `BETWEEN` Operator**

The `BETWEEN` operator is used to filter the results within a range (inclusive).

- **View students with ages between 19 and 22:**

  ```sql
  SELECT * FROM students WHERE age BETWEEN 19 AND 22;

  ```

- **View students with `dob` between '2001-01-01' and '2002-12-12':**

  ```sql
  SELECT * FROM students WHERE dob BETWEEN '2001-01-01' AND '2002-12-12';

  ```

---

### **24. `LIKE` Operator**

The `LIKE` operator is used to search for a specified pattern in a column.

- **Find names that contain 'am' at the last:**

  ```sql
  SELECT * FROM students WHERE firstName LIKE '%am';

  ```

  - `%` is a wildcard that matches zero or more characters.

- **Names starting with 'A':**

  ```sql
  SELECT * FROM students WHERE firstName LIKE 'A%';

  ```

  - `%` matches any number of characters after 'A'.

- **Case-sensitive matching with `LIKE`:**

  ```sql
  SELECT * FROM students WHERE firstname LIKE 'a%'; --will not give the names start with 'A'

  ```

- **Case-insensitive matching with `ILIKE`:**

  ```sql
  SELECT * FROM students WHERE firstname ILIKE 'a%'; --gives names starting with 'A'.

  ```

- **Third letter is 'a' (underscore `_` matches a single character):**

  ```sql
  SELECT * FROM students WHERE firstname LIKE '__a'; --e.g. Eva, Mia

  ```

- **Names with 'o' as the third letter and two characters in between:**

  ```sql
  SELECT * FROM students WHERE lastname LIKE '__o__'; --e.g. Brown, Moore

  ```

### **25. Updating and Deleting Data**

- **Update Data**

```sql
UPDATE students
SET email = 'default2@test.com', age = 30
WHERE student_id = 5;

--another example
UPDATE students SET email = NULL WHERE student_id = 4;

```

- **Delete Data**

```sql

DELETE FROM students WHERE grade = 'B';  -- Delete all B grade students
DELETE FROM students WHERE grade = 'C+' AND country = 'Ireland';  -- Specific condition

```

- **Delete All Rows**

```sql
DELETE FROM students;  -- Deletes all rows in the table

```

---

### **26. Pagination (LIMIT and OFFSET)**

- **Limit Results**

```sql
SELECT * FROM students LIMIT 5;  -- Shows first 5 rows
SELECT * FROM students LIMIT 5 OFFSET 2;  -- Skips the first 2, shows next 5 rows

```

- **Pagination Example**

```sql
SELECT * FROM students LIMIT 5 OFFSET 5 * 0;  -- Rows 1-6
SELECT * FROM students LIMIT 5 OFFSET 5 * 1;  -- Rows 7-11
SELECT * FROM students LIMIT 5 OFFSET 5 * 2;  -- Rows 12-17

```

---
