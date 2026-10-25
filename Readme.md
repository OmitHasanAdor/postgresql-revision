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
