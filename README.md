# How to create a Blazor Web App for SQL Server CRUD

## 1. Run a Docker Container with SQL Server

Install and run Docker Desktop

Open a command prompt window and run this command:

```
docker run ^  -e "ACCEPT_EULA=Y" ^  -e "MSSQL_SA_PASSWORD=Luiscoco123456" ^  -p 1433:1433 ^  -d mcr.microsoft.com/mssql/server:2022-latest
```

![image](https://github.com/user-attachments/assets/8fdf1a18-5dd9-4ef7-b127-0f8b198c866a)

## 2. Connect to SQL Server Management Studio (SSMS)

Run SSMS and input the connection data

Password is: Luiscoco123456

![image](https://github.com/user-attachments/assets/e240dd9f-3697-429b-a744-aa5378cb82bd)

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

## 6. Create a Blazor Web Application in Visual Studio 2022 Community Edition

## 7. Add the Nuget packages

Install the required NuGet packages:

Microsoft.EntityFrameworkCore.SqlServer

Microsoft.EntityFrameworkCore.Tools

Microsoft.AspNetCore.Components.Authorization

![image](https://github.com/user-attachments/assets/3db54d42-ac83-44b8-a998-314726ed19c0)

## 8. Create two new folders Data and Services

![image](https://github.com/user-attachments/assets/772fe810-5348-49ed-bf20-3be75a529ded)

## 9. Add the ApplicationDbContext

Create a new c# class in the Data folder

![image](https://github.com/user-attachments/assets/6ece1384-4837-4ff3-a666-9acdd3458d29)

We input the source code for the ApplicationDbContext.cs

```csharp
using Microsoft.EntityFrameworkCore;

namespace SQLServerCRUD.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }

        public DbSet<Product> Products { get; set; }
    }
}
```

## 10. Add the Data Model 

Create the Product class

![image](https://github.com/user-attachments/assets/16ebd8ab-1fa5-47cc-90c7-3c09889de9ed)

Add the following source code

```csharp
namespace SQLServerCRUD.Data
{
    public class Product
    {
        public int ProductId { get; set; }
        public string ProductName { get; set; }
        public decimal Price { get; set; }
        public int Quantity { get; set; }
    }
}
```

## 11. Add the Service

Add the ProductService.cs file 

![image](https://github.com/user-attachments/assets/a34c83b0-ef05-4a72-9338-bee03bcdc2b9)

Add the following source code

```csharp
using SQLServerCRUD.Data;
using Microsoft.EntityFrameworkCore;

namespace SQLServerCRUD.Services
{
    public class ProductService
    {
        private readonly ApplicationDbContext _context;

        public ProductService(ApplicationDbContext context)
        {
            _context = context;
        }

        public async Task<List<Product>> GetProductsAsync()
        {
            return await _context.Products.ToListAsync();
        }

        public async Task<Product> GetProductByIdAsync(int id)
        {
            return await _context.Products.FindAsync(id);
        }

        public async Task AddProductAsync(Product product)
        {
            _context.Products.Add(product);
            await _context.SaveChangesAsync();
        }

        public async Task UpdateProductAsync(Product product)
        {
            _context.Products.Update(product);
            await _context.SaveChangesAsync();
        }

        public async Task DeleteProductAsync(int id)
        {
            var product = await _context.Products.FindAsync(id);
            if (product != null)
            {
                _context.Products.Remove(product);
                await _context.SaveChangesAsync();
            }
        }
    }
}
```

## 12. Add the database connection string in the appsettings.json file

Edit the appsettings.json file and add the database connection string

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft": "Information",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "DetailedErrors": true,
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost,1433;Database=BlazorAppDB;User Id=sa;Password=Luiscoco123456;Trusted_Connection=False;TrustServerCertificate=True;"
  },
  "AllowedHosts": "*"
}
```

## 13. Update the middleware (Program.cs)

Include the services:

```csharp
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

and

```
builder.Services.AddScoped<ProductService>();
```

This is the Program.cs whole code:

```csharp
using Microsoft.EntityFrameworkCore;
using SQLServerCRUD.Components;
using SQLServerCRUD.Data;
using SQLServerCRUD.Services;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

// Add services to the container.
builder.Services.AddRazorComponents()
    .AddInteractiveServerComponents();
builder.Services.AddScoped<ProductService>();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error", createScopeForErrors: true);
    // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseAntiforgery();

app.MapRazorComponents<App>()
    .AddInteractiveServerRenderMode();

app.Run();
```

## 14. Update the _Imports.razor 

```
@using System.Net.Http
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.Forms
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.Web
@using static Microsoft.AspNetCore.Components.Web.RenderMode
@using Microsoft.AspNetCore.Components.Web.Virtualization
@using Microsoft.JSInterop
@using SQLServerCRUD
@using SQLServerCRUD.Components
@using SQLServerCRUD.Data
@using SQLServerCRUD.Services
```


## 15. Run the application and verify the results


