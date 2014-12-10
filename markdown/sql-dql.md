name: inverse
layout: true
class: center, middle, inverse
.indexlink[[<i class="fa fa-home"></i>](#) [<i class="fa fa-list"></i>](#index)]

---

name: normal
layout: true
class: left, middle
.indexlink[[<i class="fa fa-home"></i>](#) [<i class="fa fa-list"></i>](#index)]

---

template:inverse
# SQL

## Data Query Language

<a href="http://www.fe.up.pt/~arestivo">André Restivo</a>

---

template:inverse
name:index
# Index

.indexlist[
1. [Introduction](#intro)
1. [Selecting Data](#select)
1. [Choosing Columns](#columns)
1. [Filtering Rows](#rows)
1. [Set Operators](#set)
1. [Joining Tables](#joining)
1. [Aggregating Data](#aggregating)
1. [Sorting Rows](#sorting)
1. [Limiting Data](#limiting)
1. [Nested Queries](#nested)
1. [Text Operators](#text)
1. [Execution Order](#order)

]

---

template: inverse
name: intro
# Introduction

---

#SQL

* **S**tructured **Q**uery **L**anguage.
* A special purpose language to manage data stored in a **relational** database.
* Based on **relational algebra**.
* Pronounced *Sequel*

---

# History

* Early **70's** SEQUEL Developed at IBM
* **1986** SQL-86 and SQL-87 Ratified by ANSI and ISO.
* **1989** SQL-89
* **1992** SQL-92 Also know as SQL2.
* **1999** SQL:1999 Also known as SQL3 Includes regurlar expressions, recursive queries, triggers, non-scalar data types and some object-oriented expressions.
* **2003** SQL:2003 XML support and auto-generated values.
* **2006** SQL:2006 XQuery support.
* **2008** SQL:2008.
* **2011** SQL:2011.

---

# Standard

* Although SQL is an ANSI/ISO standard, every database system implements it in a slightly different way.
* These slides will try to adhere to the standard as much as possible.
* Sometimes we'll deviate and talk specifically about **PostgreSQL**.

---

template: inverse
name: select
# Selecting Data

---

# SELECT and FROM

* **SELECT** and **FROM** are the most basic SQL query operators.
* They allows to specify which tables (FROM) and columns (SELECT) we want to retrieve from the database.
* The result of an SQL query is also a table.

---

# Selecting all columns

To select all columns from a table we can use an *

```sql
SELECT * FROM employee;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|1 | John Doe    | 1000 | 200 | 1
|2 | Jane Doe    | 800  | 100 | 2
|3 | John Smith  | 1200 | 350 | 2
|4 | Jane Roe    | 1000 | 200 | 3
|5 | Richard Roe | 900  | 0   | NULL
]

.box_info[Selects all columns from table employee]

---

template: inverse
name: columns
# Choosing columns

---

# Choosing columns

We can select only some columns

```sql
SELECT id, name FROM employee;
```

.sqltable[
|id|name
|-|-|
|1 | John Doe    | 
|2 | Jane Doe    | 
|3 | John Smith  | 
|4 | Jane Roe    | 
|5 | Richard Roe | 
]

.box_info[Selects columns id and name from table employee]

---

# Column operations

We can also perform any operations between columns

```sql
SELECT id, name, salary - taxes FROM employee;
```

.sqltable[
|id|name|salary - taxes
|-|-|
|1 | John Doe    | 800
|2 | Jane Doe    | 700
|3 | John Smith  | 850
|4 | Jane Roe    | 800
|5 | Richard Roe | 900
]

.box_info[Selects columns id, name and the difference between salary and taxes from table employee]

---

# Renaming columns

Any column can be renamed using the **AS** operator

```sql
SELECT id AS num, name, salary - taxes AS net_salary FROM employee;
```

.sqltable[
|num|name|net_salary
|-|-|
|1 | John Doe    | 800
|2 | Jane Doe    | 700
|3 | John Smith  | 850
|4 | Jane Roe    | 800
|5 | Richard Roe | 900
]

.box_info[Renaming column id as num and the difference between salary and taxes to net_salary]

---


template: inverse
name: rows
# Filtering Rows

---

# WHERE

* The **WHERE** command allows us to filter which rows we want in our result table according to a condition.
* The condition can use any comparison operator (<, >, <=, <>, ...) and can be composed using AND, OR and NOT.

---
# Example

```sql
SELECT * FROM employee WHERE dep_num = 2 OR salary <= 900;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|2 | Jane Doe    | 800  | 100 | 2
|3 | John Smith  | 1200 | 350 | 2
|5 | Richard Roe | 900  | 0   | NULL
]

.box_info[Employees from department 2 or a salary lower or equal to 900]

---

# Example

To test if a value is null, we have to use the special **IS NULL** operator.

```sql
SELECT * FROM employee WHERE dep_num IS NULL;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|5 | Richard Roe | 900  | 0   | NULL
]

.box_info[Employees from department 2 or a salary lower or equal to 900]

Use **IS NOT NULL** to select rows where a certain attribute is not null.

---

# Removing duplicates

We can remove duplicates from the final result by using the DISTINCT operator

```sql
SELECT DISTINCT salary FROM employee;
```

.sqltable[
|salary
|-|-|
|1000
|800
|1200
|900
]

.box_info[Selects the different salaries in the database]

---
template: inverse
name: set
# Set operators

---

# Set operators

Two tables are compatible for being used in a set operation if they have the same **number of columns** and the **type** of each column is **compatible**:

* The **UNION** between two tables (R1 and R2) is a table that includes all lines that are present in R1 or R2.
* The **INTERSECT**ion between two tables (R1 and R2) is a table that includes all lines that are present in R1 and R2.
* The difference (**EXCEPT**) between two tables (R1 and R2) is a table that includes all lines that are present in R1 but not in R2.

With all these operators, **duplicate** rows are **eliminated** automatically.

---

# Union, Intersection AND Difference

![](../assets/sql/setoperators.png)

---

# Example

```sql
SELECT * FROM employee WHERE salary >= 1000;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|1 | John Doe    | 1000 | 200 | 1
|3 | John Smith  | 1200 | 350 | 2
|4 | Jane Roe    | 1000 | 200 | 3
]

```sql
SELECT * FROM employee WHERE id_dep = 2;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|2 | Jane Doe    | 800  | 100 | 2
|3 | John Smith  | 1200 | 350 | 2
]

---

# Union

```sql
SELECT * FROM employee WHERE salary >= 1000;
UNION
SELECT * FROM employee WHERE id_dep = 2;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|1 | John Doe    | 1000 | 200 | 1
|2 | Jane Doe    | 800  | 100 | 2
|3 | John Smith  | 1200 | 350 | 2
|4 | Jane Roe    | 1000 | 200 | 3
]

.box_info[Employees that have a salary larger or equal to 1000 or work on department 2]

---

# Intersection

```sql
SELECT * FROM employee WHERE salary >= 1000;
INTERSECT
SELECT * FROM employee WHERE id_dep = 2;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|3 | John Smith  | 1200 | 350 | 2
]

.box_info[Employees that have a salary larger or equal to 1000 and work on department 2]

---

# Difference

```sql
SELECT * FROM employee WHERE salary >= 1000;
EXCEPT
SELECT * FROM employee WHERE id_dep = 2;
```

.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|1 | John Doe    | 1000 | 200 | 1
|4 | Jane Roe    | 1000 | 200 | 3
]

.box_info[Employees that have a salary larger or equal to 1000 and do not work on department 2]

---

template: inverse
name: joining
# Joining Tables

---

# Cartesian product

* The cartesian product allows us to combine rows from **different tables**.
* To use it, we just have to indicate which tables we want to combine using commas to separate them.
* The result is a table containing the columns of all tables and **all possible combinations** of rows.
* Also known as **CROSS JOIN**.

---

# Example

```sql
SELECT * FROM employee;
```

.smaller.sqltable[
|id|name|salary|taxes|dep_num
|-|-|
|1 | John Doe    | 1000 | 200 | 1
|2 | Jane Doe    | 800  | 100 | 2
|3 | John Smith  | 1200 | 350 | 2
|4 | Jane Roe    | 1000 | 200 | 3
|5 | Richard Roe | 900  | 0   | NULL
]

```sql
SELECT * FROM department;
```

.smaller.sqltable[
|num|name
|-|-|
|1 | Marketing
|2 | Sales
|3 | Production
]

---

# Cartesian product

```sql
SELECT * FROM employee, department;
```

```sql
SELECT * FROM employee CROSS JOIN department;
```

.smaller.sqltable[
|id|name|salary|taxes|dep_num|num|name
|-|-|
|1 | John Doe    | 1000 | 200 | 1    |1 | Marketing
|2 | Jane Doe    | 800  | 100 | 2    |1 | Marketing
|3 | John Smith  | 1200 | 350 | 2    |1 | Marketing
|4 | Jane Roe    | 1000 | 200 | 3    |1 | Marketing
|5 | Richard Roe | 900  | 0   | NULL |1 | Marketing
|1 | John Doe    | 1000 | 200 | 1    |2 | Sales
|2 | Jane Doe    | 800  | 100 | 2    |2 | Sales
|3 | John Smith  | 1200 | 350 | 2    |2 | Sales
|4 | Jane Roe    | 1000 | 200 | 3    |2 | Sales
|5 | Richard Roe | 900  | 0   | NULL |2 | Sales
|1 | John Doe    | 1000 | 200 | 1    |3 | Production
|2 | Jane Doe    | 800  | 100 | 2    |3 | Production
|3 | John Smith  | 1200 | 350 | 2    |3 | Production
|4 | Jane Roe    | 1000 | 200 | 3    |3 | Production
|5 | Richard Roe | 900  | 0   | NULL |3 | Production
]

---

# Solving ambiguities

When selecting from more than one table, columns with the same name might lead to ambiguities. 

```sql
SELECT id, name, name FROM employee, department;
```

To solve them we must use the table name before the column name.

```sql
SELECT id, employee.name, department.name FROM employee, department;
```

---

# Solving ambiguities

```sql
SELECT id, employee.name, department.name FROM employee, department;
```

.smaller.sqltable[
|id|name|name
|-|-|
|1 | John Doe    | Marketing
|2 | Jane Doe    | Marketing
|3 | John Smith  | Marketing
|4 | Jane Roe    | Marketing
|5 | Richard Roe | Marketing
|1 | John Doe    | Sales
|2 | Jane Doe    | Sales
|3 | John Smith  | Sales
|4 | Jane Roe    | Sales
|5 | Richard Roe | Sales
|1 | John Doe    | Production
|2 | Jane Doe    | Production
|3 | John Smith  | Production
|4 | Jane Roe    | Production
|5 | Richard Roe | Production
]

---

# Joining using WHERE

```sql
SELECT * FROM employee, department WHERE dep_num = num;
```

.smaller.sqltable[
|id|name|salary|taxes|dep_num|num|name
|-|-|
|1 | John Doe    | 1000 | 200 | 1    |1 | Marketing
|~~2~~ | ~~Jane Doe~~    | ~~800~~  | ~~100~~ | ~~2~~    |~~1~~ | ~~Marketing~~
|~~3~~ | ~~John Smith~~  | ~~1200~~ | ~~350~~ | ~~2~~    |~~1~~ | ~~Marketing~~
|~~4~~ | ~~Jane Roe~~    | ~~1000~~ | ~~200~~ | ~~3~~    |~~1~~ | ~~Marketing~~
|~~5~~ | ~~Richard Roe~~ | ~~900~~  | ~~0~~   | ~~NULL~~ |~~1~~ | ~~Marketing~~
|~~1~~ | ~~John Doe~~    | ~~1000~~ | ~~200~~ | ~~1~~    |~~2~~ | ~~Sales~~
|2 | Jane Doe    | 800  | 100 | 2    |2 | Sales
|3 | John Smith  | 1200 | 350 | 2    |2 | Sales
|~~4~~ | ~~Jane Roe~~    | ~~1000~~ | ~~200~~ | ~~3~~    |~~2~~ | ~~Sales~~
|~~5~~ | ~~Richard Roe~~ | ~~900~~  | ~~0~~   | ~~NULL~~ |~~2~~ | ~~Sales~~
|~~1~~ | ~~John Doe~~    | ~~1000~~ | ~~200~~ | ~~1~~    |~~3~~ | ~~Production~~
|~~2~~ | ~~Jane Doe~~    | ~~800~~  | ~~100~~ | ~~2~~    |~~3~~ | ~~Production~~
|~~3~~ | ~~John Smith~~  | ~~1200~~ | ~~350~~ | ~~2~~    |~~3~~ | ~~Production~~
|4 | Jane Roe    | 1000 | 200 | 3    |3 | Production
|~~5~~ | ~~Richard Roe~~ | ~~900~~  | ~~0~~   | ~~NULL~~ |~~3~~ | ~~Production~~
]

---

# Joining using WHERE

```sql
SELECT * FROM employee, department WHERE dep_num = num;
```

.smaller.sqltable[
|id|name|salary|taxes|dep_num|num|name
|-|-|
|1 | John Doe    | 1000 | 200 | 1    |1 | Marketing
|2 | Jane Doe    | 800  | 100 | 2    |2 | Sales
|3 | John Smith  | 1200 | 350 | 2    |2 | Sales
|4 | Jane Roe    | 1000 | 200 | 3    |3 | Production
]

.box_info[Employees and their departments]

---

# Join using JOIN ... ON

* Instead of using a cartesian product followed by the WHERE keyword, we can use the more specific keywords: **JOIN ON**.
* These keywords allow us to specify simultaneously **which tables** to join and with which joining **condition**.
* Separates regular row filtering from joining conditions.
* Makes joining lots of tables **easier** to understand.

```sql
SELECT * FROM employee, department WHERE dep_num = num;
```

Same as:

```sql
SELECT * FROM employee JOIN department ON dep_num = num;
```

---

# Join using JOIN ... ON

```sql
SELECT * FROM employee JOIN department ON dep_num = num;
```

.smaller.sqltable[
|id|name|salary|taxes|dep_num|num|name
|-|-|
|1 | John Doe    | 1000 | 200 | 1    |1 | Marketing
|2 | Jane Doe    | 800  | 100 | 2    |2 | Sales
|3 | John Smith  | 1200 | 350 | 2    |2 | Sales
|4 | Jane Roe    | 1000 | 200 | 3    |3 | Production
]

---

# Join using JOIN ... USING

If the columns used in the join operation have the same name, we can join them using in a simpler way with JOIN USING.

```sql
SELECT * FROM employee;
```

.smaller.sqltable[
|id|name|salary|taxes|num
|-|-|
|1 | John Doe    | 1000 | 200 | 1
|2 | Jane Doe    | 800  | 100 | 2
|3 | John Smith  | 1200 | 350 | 2
|4 | Jane Roe    | 1000 | 200 | 3
|5 | Richard Roe | 900  | 0   | NULL
]

```sql
SELECT * FROM department;
```

.smaller.sqltable[
|num|name
|-|-|
|1 | Marketing
|2 | Sales
|3 | Production
]

---

# Join using JOIN ... USING

```sql
SELECT * FROM employee JOIN department USING(num);
```

.smaller.sqltable[
|id|name|salary|taxes|num|name
|-|-|
|1 | John Doe    | 1000 | 200 | 1 | Marketing
|2 | Jane Doe    | 800  | 100 | 2 | Sales
|3 | John Smith  | 1200 | 350 | 2 | Sales
|4 | Jane Roe    | 1000 | 200 | 3 | Production
]

---

# Renaming tables

* We can also rename tables.
* This might be useful if we need to use the **same table twice** in the same query.
* Or if we want to make the table names **simpler** in a complicated query.

```sql
SELECT * FROM employee;
```

.smaller.sqltable[
|id|name|sup_id
|-|-|
|1 | John Doe    | NULL
|2 | Jane Doe    | 1
|3 | John Smith  | 1
|4 | Jane Roe    | NULL
|5 | Richard Roe | 4
]

---

# Renaming tables

```sql
SELECT * FROM employee JOIN employee AS supervisor ON employee.sup_id = supervidor.id;
```

.smaller.sqltable[
|id|name|sup_id|id|name|sup_id
|-|-|
|2 | Jane Doe    | 1 |1 | John Doe    | NULL
|3 | John Smith  | 1 |1 | John Doe    | NULL
|5 | Richard Roe | 4 |4 | Jane Roe    | NULL
]

---

template: inverse
name: aggregating
# Aggregating Data

---

template: inverse
name: sorting
# Sorting Rows

---

template: inverse
name: limiting
# Limiting Data

---

template: inverse
name: nested
# Nested Queries

---

template: inverse
name: text
# Text Operators

---

template: inverse
name: order
# Execution Order
