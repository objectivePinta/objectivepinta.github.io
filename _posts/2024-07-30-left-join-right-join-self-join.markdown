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
    employee_name VARCHAR(100) NOT NULL,
    department_id INT REFERENCES departments(department_id)
);
{% endhighlight %}

And some inserts:

{% highlight sql %}

-- Insert data into the departments table
INSERT INTO departments (department_name)
VALUES
    ('Human Resources'),
    ('Engineering'),
    ('Sales');

-- Insert data into the employees table
INSERT INTO employees (employee_name, department_id)
VALUES
    ('Alice', 1),
    ('Bob', 2),
    ('Charlie', 2),
    ('David', 3),
    ('Eve', 1);
{% endhighlight %}

### INNER JOIN

Let's start from the employees table which looks like in the picture below. Notice that employee with id 7, does not have a department.

![Employees table](/pictures/joins/employees.png)


