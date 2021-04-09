# Pewlett Hackard Analysis with SQL

## Overview of Pewlett Hackard Analysis

### Purpose
The purpose of this analysis was to:
1. determine the number of retiring employees per title;
2. identify employees who are eligible to participate in a mentorship program.

## Results

### Retiring Employees by Title

To determine the number of retiring employees per title, I first created a Retirement Titles table that holds the titles of employees born between January 1, 1952 and December 31, 1955.

```
SELECT employees.emp_no,
    employees.first_name,
    employees.last_name,
    titles.title,
    titles.from_date,
    titles.to_date
INTO retirement_titles
FROM employees
LEFT JOIN titles
ON employees.emp_no = titles.emp_no
WHERE employees.birth_date BETWEEN '1952-01-01' AND '1955-12-31'
ORDER BY employees.emp_no
SELECT * FROM retirement_titles
```

I then removed duplicate rows using the DISTINCT ON statement and returned only the most recent title of each employee.

```
SELECT DISTINCT ON (retirement_titles.emp_no) retirement_titles.emp_no,
    retirement_titles.first_name,
    retirement_titles.last_name,
    retirement_titles.title
INTO unique_titles
FROM retirement_titles
ORDER BY retirement_titles.emp_no, retirement_titles.to_date DESC;
SELECT * FROM unique_titles
```

Using the COUNT function, I finally create a Retiring Titles table which shows the number of retirement age employees by job title.

```
SELECT title, COUNT(*)
INTO retiring_titles
FROM unique_titles
GROUP BY title
ORDER BY 2 DESC
SELECT * FROM retiring_titles
```

The query results produce the following table:

![Retiring Titles](./Resources/retiring_titles_table.PNG)

### Employees Eligible for Mentorship Program

To identify the employees who are eligible to participate in a mentorship program, I created a Mentorship Eligibilty table that holds the employees who were born between January 1, 1965 and December 31, 1965.

```
SELECT DISTINCT ON (employees.emp_no) employees.emp_no,
	employees.first_name,
	employees.last_name,
	employees.birth_date,
	dept_emp.from_date,
	dept_emp.to_date,
	titles.title
INTO mentorship_eligibility
FROM employees
LEFT JOIN dept_emp
ON employees.emp_no = dept_emp.emp_no
LEFT JOIN titles
ON titles.emp_no = employees.emp_no
WHERE employees.birth_date BETWEEN '1965-01-01' AND '1965-12-31'
ORDER BY employees.emp_no
SELECT * FROM mentorship_eligibility
```

The query results produce the following table:

![Mentorship Eligibility](./Resources/mentorship_eligibility_table.PNG)

Note: This table only shows the first 10 rows. The full table is 1,940 rows.

### Summary of Results
- A total of 90,398 employees are of retirement-age.
- Half of the retirees have "Engineer" in their title, making up 50.2% of the retiring population. Retirees with "Staff" in their title make up another 44.8%. The remaining 5% includes Technique Leaders and Managers.
- A total of 1,940 employees are eligible to be mentors in the mentorship program.
- There are roughly 2 mentors for every 90 vacancies.

## Pewlett Hackard Analysis Summary

90,398 roles will need to be filled as the employees begin to hit retirement age. However, we can provide additional queries to understand the impact of this more deeply. By finding the total number of employees at Pewlett Hackard, we can see the impact on the company by a percentage of how many jobs will remain filled and how many need to be filled.

We can create a query which creates a table holding all the employees at the company while also removing duplicate values by using the DISTINCT ON function and ordering the table so that it shows an employee's most recent job title. We can then write a simple query to count the number of employee IDs to get the number of total employees.

```
SELECT DISTINCT ON (employees.emp_no) employees.emp_no,
    employees.first_name,
    employees.last_name,
    titles.title,
    titles.from_date,
    titles.to_date
INTO all_employees
FROM employees
LEFT JOIN titles
ON employees.emp_no = titles.emp_no
ORDER BY employees.emp_no, titles.to_date DESC

SELECT COUNT (emp_no)
FROM all_employees
```

The query results show that there are 300,024 total employees. With this new information, we can now see that almost a third of Pewlett Hackard employees are going to be retiring, with a percentage of 30.1%.

In terms of whether there are enough employees to mentor the next generation of employees, there is approximately 2 mentors for every 90 new employees coming into Pewlett Hackard. There are not enough mentors to effectively train the next generation and I would recommend expanding the eligibilty date for mentors in order to increase the amount of mentors.

