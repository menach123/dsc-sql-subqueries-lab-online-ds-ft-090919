
# SQL Subqueries - Lab

## Introduction

Now that you've seen how subqueries work, it's time to get some practice writing them! Not all of the queries will require subqueries, but all will be a bit more complex and require some thought and review about aggregates, grouping, ordering, filtering, joins and subqueries. Good luck!  

## Objectives

You will be able to:

* Write complex queries
* Write subqueries to decompose complex queries

## CRM Database Schema

Once again, here's the schema for the CRM database you'll continue to practice with.

<img src="images/Database-Schema.png" width="600">

## Connect to the Database

As usual, start by importing the necessary packages and connecting to the database **data.sqlite**.


```python
#Your code here; import the necessary packages
import sqlite3
import pandas as pd

```


```python
#Your code here; create the connection and cursor
conn = sqlite3.Connection('data.sqlite')
cur = conn.cursor()
```


```python
def sql_(query):
    cur.execute(query)
    df = pd.DataFrame(cur.fetchall())
    df.columns = [x[0] for x in cur.description]
    return df

```

## Write an Equivalent Query using a Subquery

```SQL
select customerNumber,
       contactLastName,
       contactFirstName
       from customers
       join orders 
       using(customerNumber)
       where orderDate = '2003-01-31';
```


```python
#Your code here; use a subquery. No join will be necessary.
cur.execute("""select customerNumber,
       contactLastName,
       contactFirstName
       from customers
       where customerNumber in (select customerNumber
                                from orders
                                where orderDate = '2003-01-31');
                                """)
df = pd.DataFrame(cur.fetchall())
df.columns = [x[0] for x in cur.description]
df

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>customerNumber</th>
      <th>contactLastName</th>
      <th>contactFirstName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>141</td>
      <td>Freyre</td>
      <td>Diego</td>
    </tr>
  </tbody>
</table>
</div>



## Select the Total Number of Orders for Each Product Name

Sort the results by the total number of items sold for that product.


```python
#Your code here
query =  """select productname, 
            count(ordernumber) as totalorders, 
            sum(quantityordered) as totalquantity
            from orderdetails
            join products 
            using(productcode)
            group by 1
            order by  totalquantity desc"""
sql_(query).tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>productName</th>
      <th>totalorders</th>
      <th>totalquantity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>104</th>
      <td>1999 Indy 500 Monte Carlo SS</td>
      <td>25</td>
      <td>855</td>
    </tr>
    <tr>
      <th>105</th>
      <td>1911 Ford Town Car</td>
      <td>25</td>
      <td>832</td>
    </tr>
    <tr>
      <th>106</th>
      <td>1936 Mercedes Benz 500k Roadster</td>
      <td>25</td>
      <td>824</td>
    </tr>
    <tr>
      <th>107</th>
      <td>1970 Chevy Chevelle SS 454</td>
      <td>25</td>
      <td>803</td>
    </tr>
    <tr>
      <th>108</th>
      <td>1957 Ford Thunderbird</td>
      <td>24</td>
      <td>767</td>
    </tr>
  </tbody>
</table>
</div>



## Select the Product Name and the  Total Number of People Who Have Ordered Each Product

Sort the results in descending order.

### A quick note on the SQL  `SELECT DISTINCT` statement:

The `SELECT DISTINCT` statement is used to return only distinct values in the specified column. In other words, it removes the duplicate values in the column from the result set.

Inside a table, a column often contains many duplicate values; and sometimes you only want to list the unique values. If you apply the `DISTINCT` clause to a column that has `NULL`, the `DISTINCT` clause will keep only one NULL and eliminates the other. In other words, the DISTINCT clause treats all `NULL` “values” as the same value.


```python
#Your code here:
# Hint: because one of the tables we'll be joining has duplicate customer numbers, you should use DISTINCT
query =  """select productname, 
            count(distinct customernumber) as totalcustomer
            from orderdetails
            join products 
            using(productcode)
            join orders
            using(ordernumber)
            group by productname
            order by totalcustomer desc"""
sql_(query).head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>productName</th>
      <th>totalcustomer</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1992 Ferrari 360 Spider red</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1934 Ford V8 Coupe</td>
      <td>27</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1952 Alpine Renault 1300</td>
      <td>27</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1972 Alfa Romeo GTA</td>
      <td>27</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Boeing X-32A JSF</td>
      <td>27</td>
    </tr>
  </tbody>
</table>
</div>



## Select the Employee Number, First Name, Last Name, City (of the office), and Office Code of the Employees Who Sold Products Which Have Been Ordered by Less Then 20 people.

This problem is a bit tougher. To start, think about how you might break the problem up. Be sure that your results only list each employee once.


```python

```


```python
#Your code here
query =  """select distinct employeenumber, firstname, lastname, o.city, officecode
            from employees e
            
            join offices o
            
            using(officecode)
            
            join customers c
            
            on e.employeenumber = c.salesrepemployeenumber
            
            join orders
            
            using(customernumber)
            
            join orderdetails
            
            using(ordernumber)
            
            WHERE productCode IN (SELECT productCode
            FROM products
                                            JOIN orderdetails
                                            USING(productCode)
                                            JOIN orders
                                            USING(orderNumber)
                                            GROUP BY productCode
                                            HAVING COUNT(DISTINCT customerNumber) < 20);
            
            """
sql_(query)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>employeeNumber</th>
      <th>firstName</th>
      <th>lastName</th>
      <th>city</th>
      <th>officeCode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1370</td>
      <td>Gerard</td>
      <td>Hernandez</td>
      <td>Paris</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1501</td>
      <td>Larry</td>
      <td>Bott</td>
      <td>London</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1337</td>
      <td>Loui</td>
      <td>Bondur</td>
      <td>Paris</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1166</td>
      <td>Leslie</td>
      <td>Thompson</td>
      <td>San Francisco</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1286</td>
      <td>Foon Yue</td>
      <td>Tseng</td>
      <td>NYC</td>
      <td>3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1612</td>
      <td>Peter</td>
      <td>Marsh</td>
      <td>Sydney</td>
      <td>6</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1611</td>
      <td>Andy</td>
      <td>Fixter</td>
      <td>Sydney</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1401</td>
      <td>Pamela</td>
      <td>Castillo</td>
      <td>Paris</td>
      <td>4</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1621</td>
      <td>Mami</td>
      <td>Nishi</td>
      <td>Tokyo</td>
      <td>5</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1323</td>
      <td>George</td>
      <td>Vanauf</td>
      <td>NYC</td>
      <td>3</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1165</td>
      <td>Leslie</td>
      <td>Jennings</td>
      <td>San Francisco</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1702</td>
      <td>Martin</td>
      <td>Gerard</td>
      <td>Paris</td>
      <td>4</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1216</td>
      <td>Steve</td>
      <td>Patterson</td>
      <td>Boston</td>
      <td>2</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1188</td>
      <td>Julie</td>
      <td>Firrelli</td>
      <td>Boston</td>
      <td>2</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1504</td>
      <td>Barry</td>
      <td>Jones</td>
      <td>London</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>



## Select the Employee Number, First Name, Last Name, and Number of Customers for Employees Whose Customers Have an Average Credit Limit of Over 15K


```python
#Your code here
query = """select employeenumber, firstname, lastname, count(c.customernumber)
            from employees
            join customers c
            ON employeeNumber = c.salesRepEmployeeNumber
            group by employeenumber, firstname, lastname
            having avg(creditlimit)>15000;"""
sql_(query)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>employeeNumber</th>
      <th>firstName</th>
      <th>lastName</th>
      <th>count(c.customernumber)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1165</td>
      <td>Leslie</td>
      <td>Jennings</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1166</td>
      <td>Leslie</td>
      <td>Thompson</td>
      <td>6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1188</td>
      <td>Julie</td>
      <td>Firrelli</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1216</td>
      <td>Steve</td>
      <td>Patterson</td>
      <td>6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1286</td>
      <td>Foon Yue</td>
      <td>Tseng</td>
      <td>7</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1323</td>
      <td>George</td>
      <td>Vanauf</td>
      <td>8</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1337</td>
      <td>Loui</td>
      <td>Bondur</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1370</td>
      <td>Gerard</td>
      <td>Hernandez</td>
      <td>7</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1401</td>
      <td>Pamela</td>
      <td>Castillo</td>
      <td>10</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1501</td>
      <td>Larry</td>
      <td>Bott</td>
      <td>8</td>
    </tr>
    <tr>
      <th>10</th>
      <td>1504</td>
      <td>Barry</td>
      <td>Jones</td>
      <td>9</td>
    </tr>
    <tr>
      <th>11</th>
      <td>1611</td>
      <td>Andy</td>
      <td>Fixter</td>
      <td>5</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1612</td>
      <td>Peter</td>
      <td>Marsh</td>
      <td>5</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1621</td>
      <td>Mami</td>
      <td>Nishi</td>
      <td>5</td>
    </tr>
    <tr>
      <th>14</th>
      <td>1702</td>
      <td>Martin</td>
      <td>Gerard</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>



## Summary

In this lesson, you got to practice some more complex SQL queries, some of which required subqueries. There's still plenty more SQL to be had though; hope you've been enjoying some of these puzzles!
