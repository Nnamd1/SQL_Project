# SQL Project

## Hi there! My name is Nnamdi Leonard and you are welcome to view this project. Enjoy! 


## Introduction
This project depicts knowledge in the use of the structural query language (SQL) with the use of an SQL Server Reporting Service (SSRS)). It intends to show how I correctly translate enquiries and instructions from the team lead/manager (who for the sake of this project, we'll call Sam) into SQL statements in order to retrieve the desired data. These enquiries are broken into tasks and then segmented according to area of request. AdventureWorks2019 dataset was used for this project as a simulation.

## Goal of this Project
The aim is to report findings about transactions or records from company's stored data upon request, in order to solve a problem or reach a decision.

## Project Overview

### Task 1: This consist of unique queries in response to requests, in order to constitute the report called `Product Sales Information.`
Request: Retrieve information about the products with colour values except null, red, silver/black, white and list price between
£75 and £750. Rename the column StandardCost to Price. Also, sort the results in descending order by list price.

Query: SELECT ProductID, Name, ProductNumber, Color, StandardCost AS PRICE, ListPrice 
FROM Production.Product
WHERE Color NOT IN('NULL', 'red', 'silver/black', 'white') 
AND listprice BETWEEN 75 AND 750
ORDER BY listprice desc;

Request: Create a list of product segmentation by defining criteria that places each item in a predefined segment as follows. If
price gets less than £200 then low value. If price is between £201 and £750 then mid value. If between £750 and £1250
then mid to high value else higher value. Filter the results only for black, silver and red color products.

Query: SELECT ProductNumber, Name, ListPrice, CASE WHEN ListPrice < 200 THEN 'Low Value'
WHEN ListPrice BETWEEN 201 AND 750 THEN 'Mid Value'
WHEN ListPrice BETWEEN 750 AND 1250 THEN 'Mid to High Value'
ELSE 'Higher Value' END AS Segmentation
FROM Production.Product
WHERE Color IN ('black', 'silver', 'red');

Request: Create a list of 10 most expensive products that have a product number beginning with ‘BK’. Include only the product
ID, Name and colour.

Query: SELECT TOP(10) ProductID, Name, Color, ListPrice
FROM Production.Product
WHERE ProductNumber LIKE 'BK%'
ORDER BY ListPrice DESC;

[Here is a pictorial representation](Picture2.png) 

To view the actual report file, [click here](Product_Info.rdl)


### Task 2: This consist of unique queries in response to requests, in order to constitute the report called `Employee Details.`
Request: Find all the male employees born between 1962 to 1970 and with hire date greater than 2001 and female employees
born between 1972 and 1975 and hire date between 2001 and 2002.

Query: SELECT NationalIDNumber, Gender, YEAR(BirthDate) Birth_Year, YEAR(HireDate) Year_Hired
FROM HumanResources.Employee
WHERE Gender = 'M'
AND YEAR(BirthDate) BETWEEN 1962 AND 1970
AND YEAR(HireDate) > 2001
UNION ALL
SELECT NationalIDNumber, Gender, YEAR(BirthDate) Birth_Year, YEAR(HireDate) Year_Hired
FROM HumanResources.Employee
WHERE Gender = 'F'
AND YEAR(BirthDate) BETWEEN 1972 AND 1975 
AND YEAR(HireDate) BETWEEN 2001 AND 2002;

Request: Use employee table and calculate the ages of each employee at the time of hiring.

Query: SELECT NationalIDNumber, JobTitle, BirthDate, HireDate, DATEDIFF(YEAR, BirthDate, HireDate) Age
FROM HumanResources.Employee;

Request: How many employees will be due a long service award in the next 5 years, if long service is 20 years?

Query: SELECT COUNT(BusinessEntityID) Long_Service_Eligibility_Count
FROM HumanResources.Employee
WHERE DATEDIFF(YEAR, HireDate, GETDATE()) >= 15;

Request: How many more years does each employee have to work before reaching sentiment, if sentiment age is 65?

Query: SELECT A.NationalIDNumber, 
CONCAT(B.FirstName, ' ', B.LastName) Name, 65 - DATEDIFF(YEAR, A.BirthDate, GETDATE()) Years_ToReach_Sentiment
FROM HumanResources.Employee A
INNER JOIN Person.Person B ON A.BusinessEntityID = B.BusinessEntityID;

[Here is a pictorial representation of the report](Picture3.png)

To view the actual report file, [click here](Employee.rdl)


### Task 3: This constitutes the report called `Customer Information.`
Request: Create a list of all contact persons, where the first 4 characters of the last name are the same asthe first four characters
of the email address. Also, for all contacts whose first name and the last name begin with the same characters, create
a new column called full name combining first name and the last name only. Also provide the length ofthe new column
full name.

Query: 
-- List of contact persons whose first 4 characters of their last name = first four characters of their email
SELECT A.Title, CONCAT(A.LastName, ' ', A.FirstName) Full_Name, B.EmailAddress, A.BusinessEntityID
FROM Person.Person A
INNER JOIN Person.EmailAddress B ON A.BusinessEntityID = B.BusinessEntityID
WHERE LEFT(A.LastName, 4) = LEFT(B.EmailAddress, 4);

-- list of all contacts whose first name and the last name begin with the same characters
SELECT CONCAT(A.FirstName, ' ', A.LastName) Full_Name, LEN(CONCAT(A.FirstName, ' ', A.LastName)) Length
FROM Person.Person A
INNER JOIN Person.EmailAddress B ON A.BusinessEntityID = B.BusinessEntityID
WHERE LEFT(A.LastName, 4) = LEFT(B.EmailAddress, 4)
AND LEFT(A.LastName, 1) = LEFT(A.FirstName, 1);

[Here is a pictorial representation of the report](Picture6.png)

To view the actual report file, [click here](cust.rdl)

### Task 4: This consist of unique queries in response to requests, in order to constitute the report called `Product Pricing and Subcategories.`
Request: Implement new price policy on the product table base on the colour of the item
If white increase price by 8%, If yellow reduce price by 7.5%, If black increase price by 17.2%. If multi, silver,
silver/black or blue take the square root of the price and double the value. Column should be called Newprice. For
each item, also calculate commission as 37.5% of newly computed list price.

Query: SELECT A.*, 0.375 * A.NewPrice AS Commission FROM 
(SELECT ProductNumber, color, ListPrice, CASE WHEN Color = 'white' THEN ListPrice + (ListPrice * 0.08)
WHEN Color = 'yellow' THEN ListPrice + (ListPrice * 0.075)
WHEN Color = 'black' THEN ListPrice + (ListPrice * 0.172)
WHEN Color IN ('multi', 'silver', 'silver/black', 'blue') THEN ListPrice + (SQRT(ListPrice)*2)
ELSE ListPrice END NewPrice
FROM Production.Product) AS A;

Request:For all the products calculate
1. Commission as 14.790% of standard cost,
2. Margin, if standard cost is increased or decreased as follows:
Black: +22%, Red: -12%, Silver: +15%, Multi: +5%, White: Two times original cost divided by the square root of cost
For other colours, standard cost remains the same

Query: SELECT ProductNumber, Name Product, StandardCost, (0.1479 * StandardCost) Commission, color,
CASE WHEN Color = 'Black' THEN (StandardCost + (StandardCost * 0.22))
WHEN Color = 'Red' THEN (StandardCost - (StandardCost * 0.12))
WHEN Color = 'Silver' THEN (StandardCost + (StandardCost * 0.15))
WHEN Color = 'Multi' THEN (StandardCost + (StandardCost * 0.05))
WHEN Color = 'White' THEN ((StandardCost * 2) / SQRT(StandardCost))
ELSE StandardCost END Margin
FROM Production.Product;

Request: Create a view to find out the top 5 most expensive products for each colour

Query: CREATE VIEW top_5_expensive_products AS
SELECT color, name Product, Price, Row_num
FROM (
SELECT color, name, ListPrice Price,
ROW_NUMBER() OVER(PARTITION BY color ORDER BY ListPrice Desc) Row_num
FROM Production.Product --used on builder
) A
WHERE Row_num <= 5;

[Here is a pictorial representation of the report]()

To view the actual report file, [click here]()

