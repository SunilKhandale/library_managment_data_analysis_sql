# library_managment_data_analysis_sql
![library management data analysis](https://github.com/SunilKhandale/library_managment_data_analysis_sql/blob/main/66982lms.webp)

## objective 
1. Set up the Library Management System Database: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. CRUD Operations: Perform Create, Read, Update, and Delete operations on the data.
3. CTAS (Create Table As Select): Utilize CTAS to create new tables based on query results.
4. Advanced SQL Queries: Develop complex queries to analyze and retrieve specific data.

## project struture
![library er diagram](https://github.com/SunilKhandale/library_managment_data_analysis_sql/blob/main/Screenshot%20(48).png)
## database and table creation

## schema
```sql
create database lib;
use lib;

drop table if exists books;
create table books(
 isbn varchar(20) primary key,
 book_title varchar(20),
 category varchar(25),
 rental_price float,
 status varchar(15),
 author varchar(35),
 publisher varchar(60)
);
select count(*) from books;

drop table if exists branch;
create table branch(
branch_id varchar(10) primary key,
	emp_id varchar(10),
	branch_address varchar(55),
	contact_no int
);
select count(*) from branch;


drop table if exists issued_status;
create table issued_status(
issued_id varchar(10) primary key, 
	issued_member_id varchar(10) ,
	issued_book_name varchar(30),
	issued_date date,
	issued_book_isbn varchar(20),
	issued_emp_id varchar(10)
);
select count(*) from issued_status;

drop table if exists employees;
create table employees(
emp_id varchar(5) primary key,
	emp_name varchar(15),
	position varchar(5),
	salary int,
	branch_id varchar(15)
);
select count(*) from employees;

drop table if exists members;
create table members(
member_id varchar(10) primary key,
member_name	varchar(13),
member_address varchar(50),
reg_date date
);
select count(*) from members;

drop table if exists return_status;
create table return_status(
return_id varchar(10) primary key,
issued_id varchar(10),
return_book_name varchar(30),
return_date date,
return_book_isbn varchar(30)
);
select count(*) from return_status;
```
