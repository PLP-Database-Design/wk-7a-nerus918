# 📝 Assignment: Database Design and Normalization

## 🎯 **Learning Objectives**
* 🛠️ **Understand the principles of good database design** and **normalization**.
* 💡 **Apply normalization techniques** to improve database structure and efficiency.
* 🔍 **Learn First, Second, and Third Normal Forms** (1NF, 2NF, 3NF) to eliminate redundancy and optimize data storage.

---
## 📋 **What You'll Need**
* 💻 A computer with internet access.
* ✍️ A code editor (e.g., Visual Studio Code).
* 🖥️ MySQL Workbench or another SQL database environment.
---

## 📝 Submission Instructions  
📂 Write all your SQL queries in the **answers.sql** file.  
✍️ Answer each question concisely and make sure your queries are clear and correct.  
🗣️ Structure your responses clearly, and use comments if necessary to explain your approach.

--- 

## 📚 Assignment Questions

---

### Question 1 Achieving 1NF (First Normal Form) 🛠️
Task:
- You are given the following table **ProductDetail**:

| OrderID | CustomerName  | Products                        |
|---------|---------------|---------------------------------|
| 101     | John Doe      | Laptop, Mouse                   |
| 102     | Jane Smith    | Tablet, Keyboard, Mouse         |
| 103     | Emily Clark   | Phone                           |


- In the table above, the **Products column** contains multiple values, which violates **1NF**.
- **Write an SQL query** to transform this table into **1NF**, ensuring that each row represents a single product for an order

-- Create a normalized version of the table
SELECT 
    OrderID,
    CustomerName,
    TRIM(Product.value) AS Product
FROM 
    ProductDetail
CROSS APPLY 
    STRING_SPLIT(Products, ',') AS Product;

    OrderID | CustomerName | Product
--------|--------------|---------
101     | John Doe     | Laptop
101     | John Doe     | Mouse
102     | Jane Smith   | Tablet
102     | Jane Smith   | Keyboard
102     | Jane Smith   | Mouse
103     | Emily Clark  | Phone
--- 

CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    CustomerName VARCHAR(100) NOT NULL
);

CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName VARCHAR(100) NOT NULL
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT NOT NULL,
    OrderDate DATE,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

CREATE TABLE OrderDetails (
    OrderDetailID INT PRIMARY KEY,
    OrderID INT NOT NULL,
    ProductID INT NOT NULL,
    Quantity INT DEFAULT 1,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);

CustomerID | CustomerName
-----------|-------------
1          | John Doe
2          | Jane Smith
3          | Emily Clark

ProductID | ProductName
----------|------------
1         | Laptop
2         | Mouse
3         | Tablet
4         | Keyboard
5         | Phone

OrderID | CustomerID | OrderDate
--------|------------|----------
101     | 1          | 2023-01-01
102     | 2          | 2023-01-02
103     | 3          | 2023-01-03

OrderDetailID | OrderID | ProductID | Quantity
--------------|---------|-----------|---------
1             | 101     | 1         | 1
2             | 101     | 2         | 1
3             | 102     | 3         | 1
4             | 102     | 4         | 1
5             | 102     | 2         | 1
6             | 103     | 5         | 1

SELECT 
    OrderID,
    CustomerName,
    TRIM(Product.value) AS Product
FROM 
    ProductDetail
CROSS APPLY 
    STRING_SPLIT(Products, ',') AS Product;

    OrderID	CustomerName	Product
101	John Doe	Laptop
101	John Doe	Mouse
102	Jane Smith	Tablet
102	Jane Smith	Keyboard
102	Jane Smith	Mouse
103	Emily Clark	Phone


### Question 2 Achieving 2NF (Second Normal Form) 🧩

- You are given the following table **OrderDetails**, which is already in **1NF** but still contains partial dependencies:

| OrderID | CustomerName  | Product      | Quantity |
|---------|---------------|--------------|----------|
| 101     | John Doe      | Laptop       | 2        |
| 101     | John Doe      | Mouse        | 1        |
| 102     | Jane Smith    | Tablet       | 3        |
| 102     | Jane Smith    | Keyboard     | 1        |
| 102     | Jane Smith    | Mouse        | 2        |
| 103     | Emily Clark   | Phone        | 1        |

- In the table above, the **CustomerName** column depends on **OrderID** (a partial dependency), which violates **2NF**. 

- Write an SQL query to transform this table into **2NF** by removing partial dependencies. Ensure that each non-key column fully depends on the entire primary key.

-- Step 1: Create the Orders table (removes the partial dependency)
CREATE TABLE Orders AS
SELECT DISTINCT 
    OrderID, 
    CustomerName
FROM 
    OrderDetails;

-- Step 2: Create the OrderItems table (contains only full dependencies)
CREATE TABLE OrderItems AS
SELECT 
    OrderID, 
    Product, 
    Quantity
FROM 
    OrderDetails;

    Orders Table:
OrderID	CustomerName
101	    John Doe
102	    Jane Smith
103	    Emily Clark
OrderItems Table:
OrderID	Product	Quantity
101	    Laptop	2
101	    Mouse	1
102	    Tablet	3
102    	Keyboard	1
102	    Mouse	2
103	    Phone	1


CREATE TABLE Orders AS
SELECT DISTINCT 
    OrderID, 
    CustomerName
FROM 
    OrderDetails;

    OrderID	CustomerName
101	John Doe
102	Jane Smith
103	Emily Clark

CREATE TABLE OrderItems AS
SELECT 
    OrderID, 
    Product, 
    Quantity
FROM 
    OrderDetails;

    OrderID	Product	Quantity
101	Laptop	2
101	Mouse	1
102	Tablet	3
102	Keyboard	1
102	Mouse	2
103	Phone	1


---
Good luck 🚀
