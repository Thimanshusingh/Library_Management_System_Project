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

**Task 4: Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'.**

```sql
SELECT 
    *
FROM
    issued_status
WHERE
    issued_emp_id = 'E101';
```

**Task 5: List Members Who Have Issued More Than One Book -- Objective: Use GROUP BY to find members who have issued more than one book.**

```sql
SELECT 
    issued_emp_id, COUNT(issued_id) as total_book_issued
FROM
    issued_status
GROUP BY issued_emp_id
HAVING COUNT(issued_emp_id) > 1;
```

## 3. CTAS (Create Table As Select)

**Task 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt.**

```sql
CREATE TABLE books_count AS SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS num_issued FROM
    books AS b
        JOIN
    issued_status AS ist ON ist.issued_book_isbn = b.isbn
GROUP BY 1 , 2;
SELECT 
    *
FROM
    books_count;
```

## 4. Data Analysis & Findings

### The following SQL queries were used to address specific questions:

**Task 7. Retrieve All Books in a Specific Category.**

```sql
SELECT 
    *
FROM
    books
WHERE
    category = 'Children';
```

**Task 8: Find Total Rental Income by Category.**

```sql
SELECT 
    b.category, SUM(b.rental_price) AS Total_Rental_Income
FROM
    books AS b
        JOIN
    issued_status AS ist ON ist.issued_book_isbn = b.isbn
GROUP BY b.category;
```

**Task 9: List Members Who Registered in the Last 360 Days.**

```sql
SELECT 
    member_name, reg_date
FROM
    members
WHERE
    reg_date >= current_date() - INTERVAL 360 DAY;
```

**Task 10: List Employees with Their Branch Manager's Name and their branch details.**

```sql
SELECT 
    e1.*, b.manager_id, e2.emp_name AS manager
FROM
    employees AS e1
        JOIN
    branch AS b ON b.branch_id = e1.branch_id
        JOIN
    employees AS e2 ON b.manager_id = e2.emp_id;
```

**Task 11. Create a Table of Books with Rental Price Above a Certain Threshold 7 USD.**

```sql
CREATE TABLE expensive_books AS SELECT * FROM
    books
WHERE
    rental_price > 7;

SELECT 
    *
FROM
    expensive_books;
```

**Task 12: Retrieve the List of Books Not Yet Returned.**

```sql
SELECT DISTINCT
    ist.issued_book_name
FROM
    issued_status AS ist
        LEFT JOIN
    return_status AS rs ON ist.issued_id = rs.issued_id
WHERE
    return_id IS NULL; 
```

## Advanced SQL Operations

**Task 13: Identify Members with Overdue Books**
Write a query to identify members who have overdue books (assume a 30-day return period). Display the member's_id, member's name, book title, issue date, and days overdue.

```sql
SELECT 
    ist.issued_member_id,
    m.member_name,
    b.book_title,
    ist.issued_date,
    CURDATE() - ist.issued_date AS over_dues_days
FROM
    issued_status AS ist
        JOIN
    members AS m ON m.member_id = ist.issued_member_id
        JOIN
    books AS b ON ist.issued_book_isbn = b.isbn
        LEFT JOIN
    return_status AS rn ON ist.issued_id = rn.issued_id
WHERE
    rn.return_date IS NULL
        AND (CURDATE() - ist.issued_date) > 30
ORDER BY 1;
```

**Task 14: Update Book Status on Return**
Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table).

```sql
DELIMITER $$

CREATE PROCEDURE add_return_records(
    IN p_return_id VARCHAR(10),
    IN p_issued_id VARCHAR(10),
    IN p_book_quality VARCHAR(15)
)
BEGIN
    DECLARE v_isbn VARCHAR(50);
    DECLARE v_book_name VARCHAR(80);

    -- Insert into return_status table
    INSERT INTO return_status(return_id, issued_id, return_date, book_quality)
    VALUES (p_return_id, p_issued_id, CURRENT_DATE, p_book_quality);

    -- Fetch the book details from issued_status
    SELECT issued_book_isbn, issued_book_name
    INTO v_isbn, v_book_name
    FROM issued_status
    WHERE issued_id = p_issued_id;

    -- Update the book's status in the books table
    UPDATE books
    SET status = 'yes'
    WHERE isbn = v_isbn;

    -- Output a notice message
    SELECT CONCAT('Thank you for returning the book: ', v_book_name) AS message;
END $$

DELIMITER ;

-- Calling Functions
CALL add_return_records ('RS138', 'IS135', 'Good');
```

**Task 15: Branch Performance Report** Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql
CREATE TABLE branch_report AS SELECT b.branch_id,
    b.manager_id,
    COUNT(ist.issued_id),
    COUNT(rn.return_id),
    SUM(rental_price) FROM
    issued_status AS ist
        JOIN
    employees AS e ON e.emp_id = ist.issued_emp_id
        JOIN
    branch AS b ON b.branch_id = e.branch_id
        LEFT JOIN
    return_status AS rn ON rn.issued_id = ist.issued_id
        JOIN
    books AS bk ON ist.issued_book_isbn = bk.isbn
GROUP BY b.branch_id , b.manager_id;

select * from branch_report;
```

**Task 16: CTAS: Create a Table of Active Members** Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 6 months.

```sql
CREATE TABLE active_members AS SELECT m.member_id,
    COUNT(ist.issued_member_id) AS total_books_issued FROM
    issued_status AS ist
        JOIN
    members AS m ON ist.issued_member_id = m.member_id
WHERE
    ist.issued_date >= CURRENT_DATE() - INTERVAL 2 MONTH
GROUP BY m.member_id
HAVING COUNT(ist.issued_member_id) >= 1
ORDER BY member_id;

select * from active_members;
```

**Task 17: Find Employees with the Most Book Issues Processed** Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.

```sql
SELECT 
    e.emp_name,
    b.branch_id,
    COUNT(issued_id) AS number_of_books_issued
FROM
    employees AS e
        JOIN
    issued_status AS ist ON e.emp_id = ist.issued_emp_id
        LEFT JOIN
    branch AS b ON b.manager_id = ist.issued_emp_id
GROUP BY e.emp_name , b.branch_id
ORDER BY number_of_books_issued DESC
LIMIT 3;
```

**Task 18: Stored Procedure Objective** Create a stored procedure to manage the status of books in a library system. Description: Write a stored procedure that updates the status of a book in the library based on its issuance. The procedure should function as follows: The stored procedure should take the book_id as an input parameter. The procedure should first check if the book is available (status = 'yes'). If the book is available, it should be issued, and the status in the books table should be updated to 'no'. If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available. 

```sql
DELIMITER $$

CREATE PROCEDURE issue_book(
    IN p_issued_id VARCHAR(10),
    IN p_issued_member_id VARCHAR(10),
    IN p_issued_book_isbn VARCHAR(40),
    IN p_issued_emp_id VARCHAR(10)
)
BEGIN
    DECLARE v_status VARCHAR(10);

    -- Checking if the book is available ('yes')
    SELECT status INTO v_status 
    FROM books 
    WHERE isbn = p_issued_book_isbn;

    IF v_status = 'yes' THEN 
        -- Insert the issued book record
        INSERT INTO issued_status (issued_id, issued_member_id, issued_date, issued_book_isbn, issued_emp_id)
        VALUES (p_issued_id, p_issued_member_id, CURRENT_DATE(), p_issued_book_isbn, p_issued_emp_id);

        -- Update the book's status to 'no'
        UPDATE books
        SET status = 'no'
        WHERE isbn = p_issued_book_isbn;

        -- Output a success message
        SELECT CONCAT('Book records added successfully for book ISBN: ', p_issued_book_isbn) AS message;

    ELSE 
        -- Output an error message
        SELECT CONCAT('Sorry to inform you, the book you requested is unavailable. Book ISBN: ', p_issued_book_isbn) AS message;
    END IF;
END$$

DELIMITER ;

-- Calling Function
call issue_book('IS155', 'C102','978-0-375-41398-8', 'E104');
```

**Task 19: Create Table As Select (CTAS) Objective** Create a CTAS (Create Table As Select) query to identify overdue books and calculate fines. Description: Write a CTAS query to create a new table that lists each member and the books they have issued but not returned within 30 days. The table should include: The number of overdue books. The total fines, with each day's fine calculated at $0.50. The number of books issued by each member. The resulting table should show: Member ID Number of overdue books Total fines

```sql
CREATE TABLE books_overdue AS
SELECT 
    ist.issued_member_id,
    COUNT(ist.issued_book_name) AS total_books_issued,
    SUM(CASE 
            WHEN DATEDIFF(rn.return_date, ist.issued_date) > 30 THEN 1 
            ELSE 0 
        END) AS overdue_books,
    SUM(CASE 
            WHEN DATEDIFF(rn.return_date, ist.issued_date) > 30 THEN 
                (DATEDIFF(rn.return_date, ist.issued_date) - 30) * 0.50 
            ELSE 0 
        END) AS total_fine
FROM
    issued_status AS ist
        JOIN
    members AS m ON m.member_id = ist.issued_member_id
        LEFT JOIN
    return_status AS rn ON ist.issued_id = rn.issued_id
WHERE
    rn.return_date IS NOT NULL -- Ensure only returned books are considered
GROUP BY 
    ist.issued_member_id;

select * from books_overdue;
```
## Reports

**Database Schema:** Detailed table structures and relationships.
**Data Analysis:** Insights into book categories, employee salaries, member registration trends, and issued books.
**Summary Reports:** Aggregated data on high-demand books and employee performance.

## Conclusion

**This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.**
