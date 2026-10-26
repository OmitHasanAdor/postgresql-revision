to clear the terminal
\! cls
\list

সহজভাবে মনে রাখবে:
- Database: পুরো container বা data-এর মূল ভান্ডার।
- Schema: Database-এর ভেতরে objects-গুলোকে organize করার logical namespace।
- Table: যেখানে data rows এবং columns আকারে রাখা হয়।

Step 1: Database তৈরি করো
CREATE DATABASE school_db;
তারপর database-এ connect করো:
\c school_db
Step 2: Schema তৈরি করো
CREATE SCHEMA academic;
CREATE SCHEMA finance;

Table তৈরি করো
CREATE TABLE academic.students (
    id INTEGER PRIMARY KEY,
    name VARCHAR(100),
    age INTEGER
);

Step 4: Data insert করো
INSERT INTO academic.students
VALUES (1, 'Hasan', 22);

INSERT INTO finance.payments
VALUES (1, 1, 1500.00);

Step 5: Data দেখো
SELECT * FROM academic.students;

SELECT * FROM finance.payments;

৪. psql-এ কীভাবে দেখবে?
\l                 List all databases
\c school_db       Connect to school_db
\dn                List schemas
\dt                List tables in current search path
\dt academic.*     List tables in academic schema
\d academic.students


এই SQL query-এর কাজ হলো PostgreSQL সার্ভারে থাকা সব database-এর নাম দেখানো।
SELECT datname FROM pg_database

শুধু database-এর নাম নয়, সব তথ্য দেখতে চাইলে
SELECT * FROM pg_database;

creating table 
CREATE TABLE person(
id INT,
name VARCHAR(100),
bio VARCHAR(100)
)

person table-এর structure বা গঠন দেখাবে।
\d person

**insert values**

INSERT INTO person(id, name, bio)
VALUES (1, 'Hasan', 'I am a biggest larper');

insert values in one line
INSERT INTO person VALUES(2,'Omit','I am a developer')

multiple data insert
INSERT INTO person (id, name, bio) VALUES
(3, 'Tanvir Hossain', 'Full-stack web developer and tech enthusiast'),
(4, 'Ehsan Ahmed', 'Frontend developer specializing in React and Next.js'),
(5, 'Hamza Rahman', 'Backend engineer working with Node.js and PostgreSQL'),
(6, 'Rakib Hasan', 'UI/UX designer and creative web developer')

**reading data**

all data:
SELECT * FROM person;

shudhu column dekhte chaile
SELECT name FROM person;
<!-- SELECT <column_name> FROM person; -->
multiple column dekhte chaile
SELECT name,bio FROM person;

**updating data**
UPDATE person
SET name ='Hasan Ali'
WHERE id = 1

**delete data**
DELETE FROM person
WHERE id=12

search on browser: datatypes in postgresql
Data Type নির্ধারণ করে একটি column-এ কী ধরনের data রাখা যাবে।

Data Type	            কী ধরনের data রাখে                                  	Example

INT     	            পূর্ণসংখ্যা	                                                     25
VARCHAR(100)        	নির্দিষ্ট সীমার text	                                      'Hasan'
TEXT	                Text,            নির্দিষ্ট ছোট দৈর্ঘ্যের সীমা নেই	'Frontend Developer'
BOOLEAN	                True/False	                                                TRUE
DATE	                তারিখ	                                             '2026-10-26'
TIMESTAMP	            তারিখ ও সময়	                               '2026-10-26 10:30:00'
NUMERIC(10,2)	        নির্ভুল দশমিক সংখ্যা	                                    15000.50
UUID	                Unique identifier          '550e8400-e29b-41d4-a716-446655440000'
JSONB	                JSON data               	                '{"role":"developer"}'

Constraint হলো table-এর data-এর ওপর নির্দিষ্ট নিয়ম বা শর্ত, যা data সঠিক ও consistent রাখতে সাহায্য করে।

১. PRIMARY KEY
প্রতিটি row-কে uniquely identify করে। এর value duplicate বা NULL হতে পারে না।
id INT PRIMARY KEY

২. NOT NULL
Column-এ NULL value রাখা যাবে না।
name VARCHAR(100) NOT NULL

৩. UNIQUE
একই column-এ duplicate value রাখা যাবে না।
email VARCHAR(255) UNIQUE

৪. CHECK
নির্দিষ্ট condition পূরণ করতে হবে।
age INT CHECK (age >= 18)

৫. DEFAULT
Value না দিলে default value ব্যবহার করে।
is_active BOOLEAN DEFAULT TRUE

৬. FOREIGN KEY
অন্য table-এর সঙ্গে relationship তৈরি করে এবং reference-এর consistency বজায় রাখে।
department_id INT REFERENCES departments(id)

making a real world table
create table employees(
emp_id serial primary key,
fname varchar(100) not null,
email varchar(100) unique not null,
dept varchar(50),
salary decimal(10,2) not null default 30000.00,
hire_date date not null default current_date
)

**delete table data**
যেহেতু আগে থেকেই ১২টা employee আছে, প্রথমে এই query চালাও:
<!-- employees_emp_id_seq  eta pabo holo \d <table_name> theke -->
SELECT setval('employees_emp_id_seq', 12);

তবে আরও নিরাপদ পদ্ধতি হলো table-এর সর্বোচ্চ emp_id অনুযায়ী sequence ঠিক করা:
SELECT setval(
    'employees_emp_id_seq',
    (SELECT MAX(emp_id) FROM employees)
);

সব data delete করবে, কিন্তু table থাকবে
DELETE FROM employees;
<!-- 
সব row delete হবে।
Table-এর structure, column, constraint থাকবে।
SERIAL sequence সাধারণত reset হবে না।
 -->

 সব data দ্রুত delete করবে এবং ID sequence reset করবে
 TRUNCATE TABLE employees RESTART IDENTITY;
 <!-- 
 সব row delete হবে।
Table-এর structure থাকবে।
SERIAL-এর ID sequence reset হবে, তাই পরের insert সাধারণত emp_id = 1 থেকে শুরু হবে।
 -->

 <!-- ask ai what is data refine in psql -->

** Where clause**
select * from employees
where emp_id=8

select * from employees
where dept='HR'

<!-- conditional -->
select * from employees
where salary>=40000

<!-- with or operator -->
select * from employees
where dept='HR' or dept='Finance'

<!-- with and operator -->
select * from employees
where dept='Engineering' and salary>=50000

<!-- with not equal operator -->
select * from employees
where dept='Engineering' and salary!=45000

<!-- with in operator -->
select * from employees
where dept in('Engineering','HR','Finance')

<!-- with not in operator -->
select * from employees
where dept not in('Finance','Engineering')

<!-- with between operator -->
select * from employees
where salary between 35000 and 52000

**Distinct clause**
PostgreSQL-এ DISTINCT-এর কাজ হলো duplicate value বাদ দিয়ে শুধু unique value দেখানো।
select distinct dept from employees;

**order by**
<!-- Ascending -->
select * from employees
order by fname

<!-- descending -->
select * from employees
order by fname desc

select * from employees
order by emp_id desc


প্রথমে dept A থেকে Z ক্রমে সাজবে।
একই department-এর মধ্যে salary বেশি থেকে কম হবে।
SELECT fname, dept, salary
FROM employees
ORDER BY dept ASC, salary DESC;

**limit**
select * from employees limit 5

**like**
<!-- ager letter match korte -->
select * from employees
where fname like 'R%'
<!-- last er letter match korte egulo case sensitive-->
SELECT * FROM employees
where fname like '%m'

<!-- middle letter match korte -->
SELECT * FROM employees
where fname like '%h%'

<!-- 2ta character wala khujte -->
SELECT * FROM employees
where dept like '__'

<!-- 2nd character wala khujte -->
SELECT * FROM employees
where fname like '_a%'
