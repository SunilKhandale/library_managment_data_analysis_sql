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

## assigning the foreign keys for column
```sql
SET FOREIGN_KEY_CHECKS = 0;
alter table branch
add constraint fk_emp_id foreign key (emp_id) references employees(emp_id);
SET FOREIGN_KEY_CHECKS = 1;
select * from branch;

SET FOREIGN_KEY_CHECKS = 0;
alter table issued_status
-- add constraint fk_issued_member_id foreign key (issued_member_id) references members(member_id),
-- add constraint fk_issued_book_isbn foreign key (issued_book_isbn) references books(isbn),
add constraint fk_issued_emp_id foreign key (issued_emp_id) references employees(emp_id);
set foreign_key_checks = 1;

set foreign_key_checks = 0;
alter table return_status
add constraint fk_issued_id foreign key (issued_id) references issued_status(issued_id),
add constraint fk_return_book_isbn foreign key (return_book_isbn) references books(isbn);
set foreign_key_checks = 1;

set foreign_key_checks = 0;
alter table employees
add constraint fk_branch_id foreign key (branch_id) references branch(branch_id);
set foreign_key_checks = 1;
```

## Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql
insert into books(isbn, book_title, category, rental_price, status, author, publisher)
values('978-1-60129-456-2', 'To Kill Mockingbird', 'Classic', '6.00', 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

## Task 2: Update an Existing Member's Address
```sql
update members
set member_address = '111 main st'
where member_id = 'C101';
```

## Task 3: Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table
```sql
delete from issued_status
where issued_id = 'IS121';
```

## Task 4: Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'
```sql
select * from issued_status
where issued_emp_id = 'E101';
```

## Task 5: List Members Who Have Issued More Than One Book -- Objective: Use GROUP BY to find members who have issued more than one book. 
```sql
select * from issued_status;
drop table if exists members_book_count;
create table members_book_count as 
SELECT 
    issued_member_id,
    count(issued_id) as total_book_issued
FROM
    issued_status
GROUP BY issued_member_id
HAVING COUNT(issued_id) > 1 ;
-- check the members_book_count**
select * from members_book_count;
```

## Task 6: Create Summary Tables: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt
```sql
create table book_issued_cnt as 
select issued_book_name,
count(issued_book_isbn)
from issued_status
group by issued_book_name;

-- alternative way **
drop table if exists book_cnt1;
create table book_cnt1 as
select b.book_title, count(b.isbn) from books as b
join issued_status as ist
on ist.issued_book_isbn = b.isbn
group by 1;

select * from book_cnt1;
```

## Task 7. Retrieve All Books in a Specific Category
```sql
select * from books
where category = 'history';
```

## Task 8: Find Total Rental Income by Category
```sql
select 
b.category,
count(b.category),
sum(b.rental_price)
from books as b
join issued_status as ist
on ist.issued_book_isbn = b.isbn
group by category;
```

## List Members Who Registered in the Last 600 Days
```sql
    SELECT *
    FROM members
    WHERE reg_date >= date_sub(current_date(), interval 600 day);
    
    /* List Employees with Their Branch Manager's Name and their branch details: */
    select * from employees;
    select * from branch;
    
    select * from employees as emp
    join branch as br
    on br.branch_id = emp.branch_id
    join employees as emp_2
    on br.emp_id = emp_2.emp_id;
```    
   
## Task 11. Create a Table of Books with Rental Price Above a Certain Threshold: */
 ```sql   
    create table expensive_books as 
    select * from books
    where rental_price >= '4';
    
    select * from expensive_books;
 ```
    
## Task 12: Retrieve the List of Books Not Yet Returned */
```sql
  select * from return_status;
    
    select count(*) from books 
    where status = 'no';
    
    select count(*) from issued_status as ist
    join return_status as r
    on r.issued_id = ist.issued_id
    where r.return_id is null;
```
    
## /*advance sql*/

## /* Task 13: Identify Members with Overdue Books Write a query to identify members who have overdue books (assume a 30-day return period).
Display the member's_id, member's name, book title, issue date, and days overdue. */
```sql
select * from members; -- member_id. members_name. reg_date **
select * from issued_status; -- issued_book_name. issued_date issued_member_id **

#drop table if exists members_with_due;
#create table members_with_due as 
select m.member_id, m.member_name, ist.issued_book_name, ist.issued_date, current_date - ist.issued_date as over_due_days
from issued_status as ist
join members as m  
on  ist.issued_member_id = m .member_id 
where (current_date - ist.issued_date) > 30
order by 1;

 select * from members_with_due ;
 ```

##  /* Task 14: Update Book Status on Return Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table)*/

```sql
-- here we need to uodate thestatus as no in book table because there is no record in sttatus that hs 'no' record**
update books
set status = 'no'
where isbn = '978-0-06-112241-5';

select * from books where status = 'no';

select * from issued_status where issued_book_isbn = '978-0-06-112241-5';

select * from return_status where issued_id = 'IS125';

-- as soon as member return the book we need to add the record manually **
alter table return_status 
add book_quality varchar(25);
insert into return_status(return_id, issued_id, return_date, book_quality)
values('RS125', 'IS125', current_date(), 'good');

select * from return_status;

update books
set status = 'yes'
where isbn = '978-0-06-112241-5';

select * from books where status ='no';

/*create procedure */
drop procedure status_update;
delimiter //
create procedure status_update(in updated_status varchar(15))
begin
select * from books
where status = updated_status;
end //
delimiter ;

call status_update('yes');
-- */

/* Write a query to update the status of books in the books table to "Yes" when they are returned (based on entries in the return_status table) */
select * from books; -- status **
select * from return_status; -- return_book_isbn return_book_name **
select * from issued_status;

#drop table if exists return_book_status;
#create table return_book_status as
select r.return_book_isbn, r.return_book_name, b.status
from return_status as r
join issued_status as ist
on ist.issued_id = r.issued_id
join books as b 
on b.isbn = ist.issued_book_isbn
where b.status = 'no';

update books
set status = 'yes'
where isbn = '978-0-330-25864-8';

select * from books where status = 'no';
select * from return_book_status;
/*here we got null records in return book isbn and return book name because we have no records present in the return status table for those columns*/
```

## /*Task 15: Branch Performance Report Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals */
```sql
select * from branch; -- branch **
select * from issued_status; -- no of books issued ** ist.issued_emp_id = br.emp_id 
select * from books; -- rental_price ** ist.issued_isbn = b.isbn
select * from return_status; -- no of books return ** r.return_book_isbn = b.isbn

drop table if exists book_return;
create table book_return as 
select 
distinct br.branch_id,
b.status as book_return, 
sum(b.rental_price) as total_revenue, 
count(ist.issued_id) as no_of_books_issued
from books as b
join issued_status as ist
on ist.issued_book_isbn = b.isbn
join branch as br
on ist.issued_emp_id = br.emp_id
group by 1, 2;

select * from book_return;
```

## /* Task 16: CTAS: Create a Table of Active Members Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 2 months.*/
```sql
select * from members; -- member_id **
select * from issued_status; -- issued_date, issued_book_isbn, ist.issued_member_id = m.member_id

drop table if exists active_members;
create table active_members as
select distinct m.member_id, m.member_name, count(ist.issued_date) as active_account, ist.issued_book_isbn, ist.issued_member_id
from issued_status as ist
join members as m
on ist.issued_member_id = m.member_id
where ist.issued_date <= date_sub(curdate(), interval 2 month)
group by m.member_id, ist.issued_book_isbn;
                         
#select * from members where member_id in(select
#distinct issued_member_id from issued_status where issued_date >= date_sub(curdate(), interval 2 month));

select * from active_members;
```

## /* Task 17: Find Employees with the Most Book Issues Processed Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch */
```sql
select * from employees; -- emp_id, emp_name **
select * from branch;
select * from issued_status; -- count(issued_book_isbn), issued_book_isbn as most_book_issued, ist.issued_emp_id = e.emp_id **

#create table maximum_book_issued as
SELECT e.emp_name, b.*, COUNT(ist.issued_id) as no_book_issued
FROM issued_status as ist
JOIN
employees as e
ON e.emp_id = ist.issued_emp_id
JOIN
branch as b
ON e.branch_id = b.branch_id
GROUP BY 1, 2;

select * from maximum_book_issued;
```

## /*Task 18: Identify Members Issuing High-Risk Books Write a query to identify members who have issued books more than twice with the status "damaged" in the books table. Display the member name, book title, and the number of times they've issued damaged books.*/
```sql
update return_status
set book_quality = 'damage' # good @ IS125
where issued_id = 'IS101';

update return_status
set book_quality = 'damage'
where issued_id = 'IS103';
select * from return_status;

update return_status
set book_quality = 'damage'
where issued_id = 'IS106';

update return_status
set book_quality = 'damage'
where issued_id = 'IS109';

select * from members; -- m.member_name**
select * from books; -- book_title, no_of_times_return_damaged_book **
select * from return_status;
select * from issued_status;

create table high_risk_member as
select m.member_id, m.member_name, ist.issued_book_name, count(r.book_quality) as no_of_book_quality
from issued_status as ist
join return_status as r
on r.issued_id = ist.issued_id
join members as m
on ist.issued_member_id = m.member_id
where r.book_quality = 'damage'
group by 1, 3
order by 4 desc;

select * from high_risk_member;
```

## /* Task 19: Stored Procedure Objective: Create a stored procedure to manage the status of books in a library system. Description: Write a stored procedure that updates the status of a book in the library based on its issuance.
The procedure should function as follows: The stored procedure should take the book_id as an input parameter.
The procedure should first check if the book is available (status = 'yes').
If the book is available, it should be issued, and the status in the books table should be updated to 'no'.
If the book is not available (status = 'no'), the procedure should return an error message indicating that the book is currently not available.*/

```sql
select * from books where status = 'no';
# now we need to update somwe record to change its status to 
update books
set status = 'no'
where isbn = '978-0-06-112241-5';

update books
set status = 'no'
where isbn = '978-0-14-027526-3';

update books
set status = 'no'
where isbn = '978-0-385-33312-0';

-- 
drop procedure issue_book;
DELIMITER $$
CREATE PROCEDURE issue_book(in p_issued_id varchar(10), in p_issued_member_id varchar(10), in p_issued_book_isbn varchar(20),
in p_issued_emp_id varchar(10))
BEGIN
    DECLARE v_status VARCHAR(50);
    DECLARE result VARCHAR(100);
    select status
    into v_status
    from books
    where isbn = p_issued_book_isbn;
    
    IF v_status = 'yes' THEN
    insert into issued_status(issued_id, issued_member_id, issued_date, issued_book_isbn, issued_emp_id)
    values(p_issued_id,p_issued_member_id, curdate(), p_issued_book_isbn, p_issued_emp_id);
        SET result = 'book is available ';
	ELSE
        SET result = 'sorry the book is not available';
    END IF;
    update books
	set status = 'no'
	where isbn = p_issued_book_isbn;

    SELECT result;
END$$
DELIMITER ;
call issue_book('IS125', 'C105', '978-0-06-112241-5', 'E101'); -- for status = no 
call issue_book('IS128', 'C105', '978-0-14-027526-3', 'E110'); -- for status = no 
call issue_book('IS126', 'C105', '978-0-06-440055-8', 'E110'); -- for status = yes X X
-- call issue_book('IS112', 'C109', '978-0-09-957807-9', 'E106'); -- for status = yes X X
use lib;
commit;
rollback;
```

## Reports
1. Database Schema: Detailed table structures and relationships.
2. Data Analysis: Insights into book categories, employee salaries, member registration trends, and issued books.
3. Summary Reports: Aggregated data on high-demand books and employee performance.

## conclusion
This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis




