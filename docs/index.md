# Assignment 07 Discussion Topics
## Introduction
The following paragraphs address week 7 discussion topics for Foundations of Databases & SQL Programming:  1) When to Use a SQL UDF and 2) Differences between Scalar, Inline, and Multi-Statement Functions.
## When to Use a SQL UDF
In SQL, UDF stands for User Defined Function.  This function is stored in the database and can be called to do some work with the data such as calculations or look-ups.

A popular use of a UDF is a check constraint that looks at another table.  For example, there is an inventories table for which only counts on products priced greater than $10.00 should be added.  A UDF can go out and check the products table for the price and determine if a record can be added or not.
First, create the function dbo.fGetPrice to return the price from the products table based on a variable of the product id (Figure 1).

```
Create Function dbo.fGetPrice (@ProductID float)
Returns Float
As
Begin
Return (Select UnitPrice From Products Where ProductID = @ProductID);
End
```
#### Figure 1 - Create User Defined Function for Check Restraint

Next, add the constraint to the inventories table (Figure 2). (Note: adding the constraint will fail if there are already records in the table which violate it.)

```
Alter Table Inventories
Add Contraint ckUnitPrice
Check (dbo.fGetPrice(ProductID) > 10.00);
```
#### Figure 2 - Add Check Restraint to Table

Now to add some inventory counts: The products table in Figure 3 lists some products and prices.  Adding a count for Konbu, product id #13, should be prevented by the constraint.

![Table Values](https://github.com/mcatek/DBFoundations-Module07/blob/main/docs/Capture%20table%20values.PNG) 
#### Figure 3 - Table Values



The insert statement fails as shown in Figure 4.
```
Insert Into Inventories
(InventoryDate, EmployeeID, ProductID, ReorderLevel, Count)
Values
('2017-05-01', 5, 13, 10, 100);
go
```
add image
#### Figure 4 - Failed Insert Statement 

## Scalar, Inline and Multi-Statement Functions

A scalar function returns a single value, while both inline and multi-statement functions return tabular result sets.  The constraint check UDF back in figure one is an example of a scalar function.

An inline function returns a table based on a single Select statement and in that way is very much like a view.  The function part allows parameters to be included.  The example in Figure 5 returns a results table based on the KPI parameter.

```
Create Function dbo.fProductInventoriesWithPreviousMonthCountsWithKPEs(@Value1 Float)
Returns Table
As
Return (Select *
From vProductInventoriesWithPreviousMonthCountWithKPIs
Where (CountvsPreviousCountKPI = @Value1;
```
#### Figure 5 - Inline Function

The multi-statement function returns a table based on multiple select statements.  Essentially it creates a virtual table.  As such, the table structure must be defined, and the selected records are inserted into the virtual table.

## Summary

A SQL User Defined Function can be used in adding table constraints based on other tables or storing calculations.  The scalar function returns a single value, while the inline and multi-statement return tables.  The multi-statement requires that the table structure be defined and inserts the selected data while the inline returns a table from a single select statement.
