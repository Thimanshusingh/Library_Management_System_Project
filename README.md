# Library_Management_System_Project

## Project Overview
**Project Title:** Library Management System
**Level:** Intermediate

This project focuses on building a Library Management System using SQL, highlighting the creation and management of tables, implementing CRUD operations, and executing advanced queries. It aims to demonstrate proficiency in database design, data manipulation, and query optimization techniques.

## Objectives
**1.Set up the Library Management System Database:** Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
**2.CRUD Operations:** Perform Create, Read, Update, and Delete operations on the data.
**3.CTAS (Create Table As Select):** Utilize CTAS to create new tables based on query results.
**4.Advanced SQL Queries:** Develop complex queries to analyze and retrieve specific data.

## Project Structure

**1.Database Setup**

![Alt text](https://github.com/Thimanshusingh/Library_Management_System_Project_SQL/blob/main/Diagram.png)

**-Database Creation:** Created a database named library_project

**-Table Creation:** Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
create schema library_project;

-- Library Management System
-- Creating branch table

CREATE TABLE branch (
    branch_id VARCHAR(10) PRIMARY KEY,
    manager_id VARCHAR(10),
    branch_address VARCHAR(50),
    contact_no INT
);

-- Creating employees table

CREATE TABLE employees (
    emp_id VARCHAR(10) PRIMARY KEY,
    emp_name VARCHAR(30),
    position VARCHAR(15),
    salary INT,
    branch_id VARCHAR(10)
);

-- Creating books table

CREATE TABLE books (
    isbn VARCHAR(20) PRIMARY KEY,
    book_title VARCHAR(75),
    category VARCHAR(40),
    rental_price FLOAT,
    status VARCHAR(10),
    author VARCHAR(60),
    publisher VARCHAR(70)
);

-- Creating members table

CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(50),
    member_address VARCHAR(80),
    reg_date DATE
);

-- Creating issued_status table

CREATE TABLE issued_status (
    issued_id VARCHAR(10) PRIMARY KEY,
    issued_member_id VARCHAR(10),
    issued_book_name VARCHAR(75),
    issued_date DATE,
    issued_book_isbn VARCHAR(40),
    issued_emp_id VARCHAR(10)
);

-- Creating return_status table

CREATE TABLE return_status (
    return_id VARCHAR(10) PRIMARY KEY,
    issued_id VARCHAR(10),
    return_book_name VARCHAR(75),
    return_date DATE,
    return_book_isbn VARCHAR(20)
);

-- Foreign Key
ALTER TABLE issued_status
ADD FOREIGN KEY (issued_member_id)
REFERENCES members (member_id);

ALTER TABLE issued_status
ADD FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);

ALTER TABLE issued_status
ADD FOREIGN KEY (issued_emp_id)
REFERENCES employees (emp_id);

ALTER TABLE employees
ADD FOREIGN KEY (branch_id)
REFERENCES branch (branch_id);

ALTER TABLE return_status
ADD FOREIGN KEY (issued_id)
REFERENCES issued_status (issued_id);
```

## 2. CRUD Operations
**Create:** Inserted sample records into the books table.
**Read:** Retrieved and displayed data from various tables.
**Update:** Updated records in the employees table.
**Delete:** Removed records from the members table as needed.

**Task 1. Create a new book Record :** "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES (978-1-60129-456-2, 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

**Task 2: Update an Existing Member's Address.**

```sql
UPDATE members 
SET 
    member_address = ' 741 Main St'
WHERE
```

**Task 3: Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table.**

```sql
DELETE FROM issued_status 
WHERE
    issued_id = 'IS121';
```
