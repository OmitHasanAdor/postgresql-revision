to clear the terminal
\! cls;
*db list dekhte*
\list;
*List of tables dekhte*
\dt;
*database select kora*
\c



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

---

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

---

****clause****
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

---

****aggregate function****

**count**
select count(emp_id) from employees;

**sum**
select sum(salary) from employees;

**avg**
select avg(salary) from employees;

**minimum**
select min(salary) from employees;

**maximum**
select max(salary) from employees

****Group By****
<!-- just basic group banano -->
select dept from employees 
group by dept

****GROUP BY-এর সঙ্গে Aggregate Functions****

**count**
select dept,count(*)
from employees 
group by dept

**sum**
select dept, sum(salary)
from employees
group by dept

**avg**
select dept, avg(salary)
from employees
group by dept

**min**
select dept, min(salary)
from employees
group by dept

**WHERE আর GROUP BY একসঙ্গে**
<!-- as diye ekta temporaly name dewa jay -->
SELECT dept, COUNT(*) AS total_employees
FROM employees
WHERE salary >= 40000
GROUP BY dept;

**HAVING**
WHERE individual row filter করে, আর HAVING group তৈরি হওয়ার পর group filter করে।
SELECT dept, COUNT(*) AS total_employees
FROM employees
GROUP BY dept
HAVING COUNT(*) > 2;

---

****string functions****

*PostgreSQL-এর String Functions হলো এমন কিছু built-in function, যেগুলো দিয়ে text বা string-এর ওপর বিভিন্ন কাজ করা যায়। যেমন: বড় হাতের অক্ষর ছোট করা, text-এর দৈর্ঘ্য বের করা, দুটি string জোড়া লাগানো, text-এর অংশ বের করা ইত্যাদি।*
---

**concat**
select concat(fname, dept) from employees
<!-- space dite  -->
select concat(fname,' ', dept) from employees

<!-- better approach -->
**concat_ws** 
<!-- means concat with separater -->
select concat_ws(' ',fname, dept, salary, email)  as nameAndDept
from employees

<!-- concat er shathe onno column gulow ante -->
select emp_id, concat(fname, dept) empDetails , salary
from employees

**substring**
string-এর অংশ বের করা
<!-- এখানে position 1 থেকে 6টি character নেওয়া হয়েছে। -->
select substring('i love to code', 1,6)
<!-- majhkhan theke nile -->
select substring('i love to code', 11,14)

**REPLACE**
text-এর অংশ পরিবর্তন
<!--
 1)mull string
2)jeta replace korbo
3) jeta diye replace korbo
 -->
select replace('hey world','hey','Hello')
<!-- table er data nile -->
select replace(dept , 'Engineering', 'Tech') from employees;

**reverse**
select reverse('hello')
<!-- table er data nile -->
select reverse(dept) from employees

**length**
select length('Hasan')
<!-- table er data nile -->
select length(dept) from employees
<!-- shathe onno kono column nile  -->
select length(dept),fname from employees

<!-- condition use kore -->
select * from employees
where length(fname) > 5

**upper**
select upper(fname) from employees

**lower**
select lower(fname) from employees

**left**
বাম দিক থেকে koyekta character নেওয়া
select left('postgresql', 4)
<!-- table er data nile -->
select left(fname, 4) from employees

**right**
 ডান দিক থেকে koyekta character নেওয়া
 select right('postgresql', 4)
 <!-- table er data nile -->
 select right(dept, 4) from employees

**trim**
<!-- etar length dibe onk beshi space er karone. etake trim korte hobe. trim normally password db te insert korar time a use hoy -->
select length('     alright     ')

<!-- using trim ekhane length 7 dibe-->
select length(trim('     alright     '))

**positon**
<!-- text er porition ber kora -->
select position('om' in 'thomas')

---
***task***
concat_ws er bhitor concat_ws eta banate
 <!-- hasan@example.com Hasan:Engineering:45000.00 -->
select concat_ws(' ',email ,concat_ws(':', fname, dept, salary)) as empDetails from employees

<!-- 5:Tanvir:FINANCE -->
select concat_ws(':',emp_id,fname,upper(dept))
from employees
where emp_id=5
<!-- M6 Sadia banate hobe -->
select concat(left(dept,1),emp_id,' ',fname)
from employees
where emp_id=6

<!-- high to low salary -->
select * from employees
order by salary desc

<!-- top 3 salary -->
select * from employees
order by salary desc
limit 3

<!-- first name A -->
select * from employees
where fname like'A%'

<!-- 5 char fname find -->
select * from employees
where length(fname)=5 

<!-- total employees count -->
select count(emp_id)
from employees

<!-- no. of emp each dept -->
select dept,count(emp_id)
from employees
group by dept

<!--subquery: query er bhitore query use kore lowest salary pawa employee find kora -->
SELECT * FROM employees 
WHERE salary = (SELECT MIN(salary) FROM employees);

<!-- highest salary pawa employee find kora -->
SELECT * FROM employees 
where salary=(select max(salary) from employees)

<!-- total salary of specific dept -->
SELECT dept, sum(salary)
from employees
where dept='Engineering'
group by dept

<!-- avg salary of each dept -->
select dept, avg(salary)
from employees
group by dept

---

**Altering Table**
PostgreSQL-এ Altering Table মানে হলো আগে থেকে তৈরি করা table-এর structure পরিবর্তন করা। এর জন্য আমরা ALTER TABLE command ব্যবহার করি।

**নতুন Column যোগ করা — ADD COLUMN**
alter table users
add column age int;

<!-- null value na chaile -->
alter table users 
add column acctype varchar(50) default 'personal'

**Column মুছে ফেলা — DROP COLUMN**
alter table users 
drop column acctype

**Column-এর নাম পরিবর্তন — RENAME COLUMN**


**Column-এর Data Type পরিবর্তন — ALTER COLUMN TYPE**
alter table users
rename column acctype to accdetails;

**Table-এর নাম পরিবর্তন — RENAME TO**
alter table person
rename to person_data;

**Data type set kora**
alter table users
alter column username 
set data type varchar(50)

**Default Value যোগ বা delete করা**
alter table users
alter column age
set default 0;

<!-- delete -->
alter table users
alter column age
drop default


**NOT NULL যোগ করা**
ALTER TABLE employees
ALTER COLUMN dept SET NOT NULL;
<!-- সতর্কতা: আগে থেকেই কোনো row-তে dept-এর value NULL থাকলে এই command ব্যর্থ হবে। প্রথমে সেগুলো ঠিক করতে হবে। -->
UPDATE employees
SET dept = 'Unassigned'
WHERE dept IS NULL;


**NOT NULL সরিয়ে দেওয়া**
alter table users
alter column username
drop not null


**check constraints**
<!-- check er por amra condition use korte pari -->
alter table users
add column mob varchar(15)
check (length(mob)>=10);

**Constraint মুছে ফেলা**
alter table users
drop constraint users_username_key;


*constraint make with custom name*
alter table person_data
add constraint more_than_10 check(length(mob)between 10 and 15)

<!-- table create korar somoy constraint make with custom name -->
create table person(
name varchar(100),
email varchar(100) unique,
mob varchar(15) unique,
constraint mob_no_less_than_10 check (length(mob) between 10 and 15)
)

<!--
eta diye check korte pari
 insert into person(mob)
values(123333333333); 
 -->

*name VARCHAR(100) — সর্বোচ্চ ১০০ অক্ষরের নাম।
email VARCHAR(100) UNIQUE — একই email একাধিক row-তে রাখা যাবে না।
mob VARCHAR(15) UNIQUE — একই mobile number একাধিক row-তে রাখা যাবে না।
CONSTRAINT mob_no_less_than_10 — তোমার CHECK constraint-এর নাম।
CHECK (LENGTH(mob) BETWEEN 10 AND 15) — mobile number-এর দৈর্ঘ্য ১০ থেকে ১৫ অক্ষরের মধ্যে হতে হবে।*


---
**case**

SQL-এর CASE হলো conditional expression। এটি অনেকটা programming-এর if-else statement-এর মতো কাজ করে।

<!-- syntex -->
select fname, salary,
case
when salary>=45000 then 'High'
else 'Low'
end as sal_cat
from employees

<!-- multiple condition -->
select fname, salary,
case
when salary >=50000 then 'high'
when salary >= 40000 and salary<50000 then 'mid'
else 'low'
end as sal_cat
from employees;

<!-- case use kore 10% bonus  ber kora-->

select fname, salary,
case
when salary >0 then (round(salary*.10))
end as bonus
from employees;

<!-- যদি তোমার উদ্দেশ্য শুধু salary-এর ১০% হিসাব করা হয়, কোনো condition ছাড়াই, তাহলে CASE দরকার নেই: -->

SELECT fname, salary,
    ROUND(salary * 0.10) AS bonus
FROM employees;

<!-- using case with group by -->

select
case
when salary >=50000 then 'high'
when salary >= 40000 and salary<50000 then 'mid'
else 'low'
end as sal_cat,
<!--ekhane  as emp_count dilew hobe na dilew hobe  -->
count(*) as emp_count
<!-- count(*) er jaygay count(emp_id) o dewa jabe -->
from employees
group by sal_cat;

---

**Relationship**

Relationship-এর ৩টি প্রধান ধরন
1. One-to-One (1:1)
একটি table-এর একটি row অন্য table-এর সর্বোচ্চ একটি row-এর সঙ্গে সম্পর্কিত।
উদাহরণ: একজন ব্যক্তির একটি passport।

2. One-to-Many (1:N)
একটি table-এর একটি row অন্য table-এর অনেক row-এর সঙ্গে সম্পর্কিত হতে পারে।
উদাহরণ: একটি department-এ অনেক employee থাকতে পারে।

3. Many-to-Many (M:N)
একটি table-এর অনেক row অন্য table-এর অনেক row-এর সঙ্গে সম্পর্কিত হতে পারে।
উদাহরণ: একজন student অনেক course করতে পারে, আবার একটি course-এ অনেক student থাকতে পারে।

**Foreign Key**
Foreign Key হলো এমন একটি column, যা অন্য table-এর PRIMARY KEY বা UNIQUE column-কে reference করে দুইটি table-এর মধ্যে relationship তৈরি করে।

<!-- customer er table -->

create table customers(
cust_id serial primary key,
cust_name varchar(100) not null
)

<!-- orders er table foreign key use kore -->

create table orders(
order_id serial primary key,
order_date date  not null DEFAULT current_date,
price numeric not null,
cust_id int not null,
foreign key(cust_id) references
customers(cust_id)
)

**Joins**
1. INNER JOIN
দুই table-এ যেসব row-এর matching data আছে, শুধু সেগুলো দেখাবে।

select * from customers c
inner join orders o
on c.cust_id=o.cust_id;
<!-- alias use na kore korle -->
select * from customers
inner join orders
on customers.cust_id=orders.cust_id;

<!-- inner join with group by -->
<!-- order count ber kora -->
select customers.cust_name,count(orders.order_id) from customers
inner join orders
on customers.cust_id=orders.cust_id
group by cust_name;

<!-- order amount ber kora -->
select customers.cust_name, sum(orders.price) from customers
inner join orders
on customers.cust_id=orders.cust_id
group by cust_name;

<!-- alias use kore korle -->
select c.cust_name, sum(o.price) from customers c
inner join orders o
on c.cust_id=o.cust_id
group by cust_name;

2. LEFT JOIN
customers table-এর সব customer দেখাবে। Order থাকলে order-এর data দেখাবে, না থাকলে order-এর column-গুলোতে NULL দেখাবে।left er shob data dekhabe right er data na thakle null dekhabe .
select * from customers
left join orders
on customers.cust_id=orders.cust_id;

3. RIGHT JOIN
orders table-এর সব matching row-সহ সব order দেখাবে। Matching customer না থাকলে customer-এর column-গুলোতে NULL আসবে।
select * from orders 
right join customers
on customers.cust_id = orders.cust_id;

4. FULL OUTER JOIN
দুই table-এর সব row দেখাবে—matching থাকুক বা না থাকুক। Matching না থাকলে অপর table-এর column-গুলোতে NULL থাকবে।
SELECT
    c.cust_name,
    o.order_id,
    o.price
FROM customers AS c
FULL OUTER JOIN orders AS o
ON c.cust_id = o.cust_id;

 5. CROSS JOIN
 একটি table-এর প্রতিটি row-এর সঙ্গে অন্য table-এর প্রতিটি row combine করবে।
 select * from customers cross join orders;


**Many to Many**
<!-- 3ta table create -->
create table students(
s_id serial primary key,
name varchar(100) not null
)

create table courses(
c_id serial primary key,
name varchar(100) not null,
fee numeric not null
)

create table enrollment(
enrollment_id serial primary key,
s_id int not null,
c_id int not null,
enrollment_date date not null default current_date,
foreign key(s_id) references students(s_id),
foreign key(c_id) references courses(c_id)
)

*3 ta table ke ekshathe dekhte*
<!-- join mane default vabe inner join e bujhay -->
select s.name, c.name, c.fee, e.enrollment_date
from enrollment e
join students s on s.s_id=e.s_id
join courses c on c.c_id=e.c_id

<!-- 2ta inner join ekashthe use kore -->
SELECT
    s.name AS student_name,
    c.name AS course_name,
    c.fee,
    e.enrollment_date
FROM enrollment AS e
INNER JOIN students AS s
ON e.s_id = s.s_id
INNER JOIN courses AS c
ON e.c_id = c.c_id;

<!-- group by use korle -->
<!-- JOIN + GROUP BY + COUNT + SUM -->
select s.name,count(e.c_id),sum(c.fee)
from enrollment e
join students s on s.s_id=e.s_id
join courses c on c.c_id=e.c_id

group by s.name,s.s_id;

<!-- alias use kore group by use korle(ete output shundor hoy) -->
SELECT
    s.name AS student_name,
    COUNT(e.c_id) AS total_courses,
    SUM(c.fee) AS total_fee
FROM enrollment e
JOIN students s ON s.s_id = e.s_id
JOIN courses c ON c.c_id = e.c_id
GROUP BY s.s_id, s.name;

*task on join*
<!-- tables -->
CREATE TABLE customers (
    cust_id SERIAL PRIMARY KEY,
    cust_name VARCHAR(100) NOT NULL
);

CREATE TABLE orders (
    ord_id SERIAL PRIMARY KEY,
    ord_date DATE NOT NULL DEFAULT CURRENT_DATE,
    cust_id INT NOT NULL,
    FOREIGN KEY (cust_id) REFERENCES customers(cust_id)
);

CREATE TABLE products (
    p_id SERIAL PRIMARY KEY,
    p_name VARCHAR(100) NOT NULL,
    price NUMERIC(10,2) NOT NULL
);

CREATE TABLE ord_items (
    items_id SERIAL PRIMARY KEY,
    ord_id INT NOT NULL,
    p_id INT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),

    FOREIGN KEY (ord_id) REFERENCES orders(ord_id),
    FOREIGN KEY (p_id) REFERENCES products(p_id)
);

*4ta table er shokol data ek shathe join korte*
select * from ord_items oi
join products p
on p.p_id=oi.p_id
join orders o
on o.ord_id=oi.ord_id
join customers c
on c.cust_id=o.cust_id;

*4ta table er specific data customize kore join korte , ekhane multiply operation o kora hoyeche*
select c.cust_name,
o.ord_date,
p.p_name,
p.price,
oi.quantity,
(p.price * oi.quantity)
from ord_items oi
join products p
on p.p_id=oi.p_id
join orders o
on o.ord_id=oi.ord_id
join customers c
on c.cust_id=o.cust_id;
