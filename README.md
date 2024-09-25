# How to create a Blazor Web App for SQL Server CRUD

## 1. Run a Docker Container with SQL Server

Install and run Docker Desktop

Open a command prompt window and run this command:

```
docker run ^  -e "ACCEPT_EULA=Y" ^  -e "MSSQL_SA_PASSWORD=Luiscoco123456" ^  -p 1433:1433 ^  -d mcr.microsoft.com/mssql/server:2022-latest
```

## 2. Connect to SQL Server Management Studio (SSMS)

Run SSMS and input the connection data



## 3. Create the database 

Run this command to create a new database

```sql
CREATE DATABASE BlazorAppDB;
GO
```

and

```sql
USE BlazorAppDB;
GO
```

## 4. Create the database table

For creating the table in the database run this command:

```sql
-- Create the table for products
CREATE TABLE Products (
    ProductId INT PRIMARY KEY IDENTITY(1,1),
    ProductName NVARCHAR(100) NOT NULL,
    Price DECIMAL(18, 2) NOT NULL,
    Quantity INT NOT NULL
);
```

## 5. Seed the database with data

Populate the database with some data

```sql
-- Seed the table with sample data
INSERT INTO Products (ProductName, Price, Quantity)
VALUES ('Laptop', 1000.00, 10),
       ('Smartphone', 500.00, 50),
       ('Tablet', 300.00, 30),
       ('Headphones', 50.00, 100),
       ('Monitor', 200.00, 20);
```

## 1. Create a Blazor Web Application in Visual Studio 2022 Community Edition

## 2. Add the Nuget packages

## 3. Create two new folders Data and Services

## 4. Add the ApplicationDbContext


## 5. Add the Data Model 

## 6. Add the Service

## 7. Add the database connection string in the appsettings.json file

## 8. Update the middleware (Program.cs)

## 9. Update the _Imports.razor 

## 10. Run the application and verify the results


