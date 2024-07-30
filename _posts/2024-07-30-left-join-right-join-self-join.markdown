---
layout: post
title:  "Left Join, Right Join, Self Join"
date:   2024-07-30 05:20:30 +0300
categories: sql basic
---

It's something that I might have used once or twice in my work, but it came once in an interview so I thought I should give some more attention to it. 

To understand these special joins, I prepared some tables:

{% highlight sql %}
-- Create the departments table
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL
);

-- Create the employees table
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    manager_id INT,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT REFERENCES departments(department_id)
);
{% endhighlight %}

And some inserts and updates:

{% highlight sql %}

INSERT INTO departments (department_name)
VALUES
    ('Human Resources'),
    ('Engineering'),
    ('Sales'),
    ('Marketing');

-- Insert data into the employees table
INSERT INTO employees (employee_name, department_id, manager_id)
VALUES
    ('Alice', 1, NULL),
    ('Bob', 2, 1),
    ('Charlie', 2, 1),
    ('David', 3, 2),
    ('Eve', 1, NULL),
    ('Fred', 4, NULL),
    ('George', NULL, NULL);  
{% endhighlight %}


### INNER JOIN

Let's start from the employees table which looks like in the picture below. Notice that employee with id 7, does not have a department.

![Employees table](/pictures/joins/employees.png)

When I run the inner join, it will retrieve the employees whose departmentId has a match in the departments table. So only the ones who have a departmentId that exists in departments table, because George does not have a departmentId, he will not be part of the result

{% highlight sql %}
SELECT employees.employee_name, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;
{% endhighlight %}

And this is the result:

![Employees table](/pictures/joins/innerJoin.png)

### LEFT JOIN

This will return all rows from the left table, and the matched rows from the right table. If no match is found, it returns `NULL` for columns of the right table.

So that's how employees table looks like:

![Employees table](/pictures/joins/employees.png)

And this is the departments table:

![Departments table](/pictures/joins/departments.png)

{% highlight sql %}
SELECT employees.employee_name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id;
{% endhighlight %}

The result looks like below. Notice that in this case George is present, altough for him the department_name is `NULL`:

![Left join result](/pictures/joins/leftJoin.png)


### RIGHT JOIN

The right join will bring only employees with an assigned department (so George will not be present in the result), but also all the departments, even the ones with no employees. So when we did the `LEFT JOIN` we saw George was present in the result, even if he isn't assigned to a department, but we didn't see the departments with no employees. When we do the `RIGHT JOIN` the reverse is happening: employees with no department are not part of the result, instead departments with no employees are present.

{% highlight sql %}
SELECT employees.employee_name, departments.department_name
FROM employees
RIGHT JOIN departments ON employees.department_id = departments.department_id;
{% endhighlight %}

Result:
![Left join result](/pictures/joins/rightJoin.png)


### FULL OUTER JOIN

If we want a `RIGHT JOIN` combined with a `LEFT JOIN` we need a `FULL OUTER JOIN`, meaning this type of `JOIN` will retrieve all employees (the ones with no department assigned included) and all departments (even the ones with no employees).

{% highlight sql %}
SELECT employees.employee_name, departments.department_name
FROM employees
FULL OUTER JOIN departments ON employees.department_id = departments.department_id;
{% endhighlight %}

![Full outer join result](/pictures/joins/fullOuterJoin.png)

### SELF JOIN

Last, but not least is a `join` of the table employees done with itself, which is known as a self join. 

Basically inside the employees table, there's a manager_id column. This contain the id of the employee which is also the manager of that employee. 
![Employees table](/pictures/joins/employees.png)

As we can see in the result below, it returns all the employees (because it's a left join and employees is the left table), with or without a manager assigned.

![Self join](/pictures/joins/selfLeftJoin.png)

And that's it for my first post.
