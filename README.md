# SQL Project

## Hi there! My name is Nnamdi Leonard and you are welcome to view this project. Enjoy! 


## Introduction
This project depicts knowledge in the use of the structural query language (SQL) with the use of an SQL Server Reporting Service (SSRS)). It intends to show how I correctly translate enquiries and instructions from the team lead/manager (who for the sake of this project, we'll call Sam) into SQL statements in order to retrieve the desired data. These enquiries are broken into tasks and then segmented according to area of request. AdventureWorks2019 dataset was used for this project as a simulation.

## Goal of this Project
The aim is to report findings about transactions or records from company's stored data upon request, in order to solve a problem or reach a decision.

## Project Overview

### Task 1: This consist of three seperate queries to constitute the `Product Sales Information.`
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

[Here is a pictorial representation](SQL_Project/Picture2.png)

