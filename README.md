Lab: SQL Table Relations
Introduction

In this lab assessment, you'll practice your knowledge of JOIN statements and subqueries, using various types of joins and various methods for specifying the links between them. One of the main benefits of using a relational database is the table relations that define them, which allow you to access and connect data together via shared columns. By writing more advanced SQL queries that utilize joins and subqueries, you can provide a deeper and more granular level of analysis and data retrieval.

This assessment continues looking at the familiar Northwind database, which contains customer relationship management (CRM) data as well as employee and product data. You take a deeper dive into this database to accomplish more advanced SQL queries that require you to access data from multiple tables at once.

Imagine that you are working in an analyst role for the sales rep team. They have collaborated with the customer relations and the product teams to take a comprehensive look at the employee-to-customer pipeline in an attempt to find areas of improvement and potential growth. You have been asked to provide some specific data and statistics regarding this project.

Learning Objectives

You will be able to:

Write SQL queries that make use of various types of joins
Choose and perform whichever type of join is best for retrieving the desired data
Write subqueries to decompose complex queries
Limit the number of records returned by a query using LIMIT
Use GROUP BY statements in SQL to apply aggregate functions
Database

The database is the customer relationship management (CRM) database (data.sqlite). The key relationships between tables are:

Relationship	Shared column(s)
employees → offices	officeCode
employees → employees (manager)	reportsTo → employeeNumber
customers → employees (sales rep)	salesRepEmployeeNumber → employeeNumber
orders → customers	customerNumber
payments → customers	customerNumber
orderdetails → orders	orderNumber
orderdetails → products	productCode
products → productlines	productLine

Tip: look for the shared columns across the tables you need to join together.

Set Up
Fork and clone the GitHub repo.
Install dependencies and enter the virtual environment:
pipenv install
pipenv shell
All code goes in main.py. Run it with python3 main.py (add print statements as needed to check your work).
Check your work with the test suite by running pytest or pytest -x (stops at the first failure).

main.py starts by importing sqlite3 and pandas and connecting to the database:

python
import sqlite3
import pandas as pd

conn = sqlite3.connect('data.sqlite')

pd.read_sql("""SELECT * FROM sqlite_master""", conn)
Tasks and Approach
Part 1: Join and Filter
Boston employees (df_boston): the first and last name of every employee in the Boston office (the test expects 2 columns, so jobTitle is left out). INNER JOIN employees to offices on officeCode, then WHERE city = 'Boston'.
Offices with zero employees (df_zero_emp): LEFT JOIN from offices to employees and keep rows where the employee side is NULL.
Part 2: Type of Join
All employees with their office (df_employee): first name, last name, and the office city and state, including employees without an office. Start from employees and LEFT JOIN offices. Order by first name, then last name.
Customers with no orders (df_contacts): contact first name, last name, phone, and sales rep employee number. LEFT JOIN orders and keep rows where orderNumber IS NULL. Sort by contact last name. 24 customers are expected.
Part 3: Built-in Function
Customer payments (df_payment): contact first and last name with the payment amount and date, sorted by amount from high to low. The amount column is stored as text, so use CAST(amount AS REAL) in the ORDER BY; otherwise values sort alphabetically.
Part 4: Joining and Grouping
High-credit-limit sales reps (df_credit): employee number, first name, last name, and number of customers for employees whose customers have an average credit limit over 90k. GROUP BY employee, filter with HAVING AVG(creditLimit) > 90000, and sort by number of customers (high to low). 4 employees are expected.
Top-selling products (df_product_sold): product name, numorders (count of orders), and totalunits (sum of quantityOrdered), sorted by totalunits from high to low.
Part 5: Multiple Joins
Product market reach (df_total_customers): product name, product code, and numpurchasers, the number of distinct customers who ordered each product. Join products → orderdetails → orders and use COUNT(DISTINCT customerNumber). Sort by numpurchasers from high to low.
Customers per office (df_customers): office code, city, and n_customers. Join offices → employees → customers and group by office.
Part 6: Subquery
Under-performing products (df_under_20): employee number, first name, last name, office city, and office code for employees who sold products ordered by fewer than 20 customers. The subquery (adapted from Task 8) returns product codes with COUNT(DISTINCT customerNumber) < 20. The outer query joins employees → offices, customers, orders, and orderdetails, filters with productCode IN (subquery), uses DISTINCT to remove duplicates, and sorts by last name.

Finally, close the connection with conn.close().

Notes
Task 1 columns: the instructions mention job titles, but test_main.py checks df_boston.shape == (2, 2), so the query returns only first and last name.
Row order: Task 10 sorts by lastName so the first row is deterministic (the test expects 'Loui'). Without an ORDER BY, SQLite returns rows in whatever order its query plan produces.
Trailing whitespace: the test for Task 5 expects 'Diego ' (with a trailing space), because that is how the value is stored in the data. Do not TRIM it.
Submitting

Before you submit your solution, save your progress with git:

Stage your changes: git add .
Commit: git commit -m "Your commit message"
Push to GitHub: git push origin main or git push origin master, depending on your branch name (check with git branch).