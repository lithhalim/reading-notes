#### Relational databases and SQL
##### Relational Database:the database relation debend table related with each other 
###### SQL is the standard language for dealing with Relational Databases


<h3 align="center">SQL</h3>
#### Create Database
###### CREATE DATABASE acme;
#### Delete Database
###### DROP DATABASE acme;
#### Select Database
###### USE acme;
#### Create Table
###### CREATE TABLE users(
id INT AUTO_INCREMENT,
   first_name VARCHAR(100),
   last_name VARCHAR(100),
);

### Where Clause
###### SELECT * FROM users WHERE location='Massachusetts';
###### SELECT * FROM users WHERE location='Massachusetts' AND dept='sales';
###### SELECT * FROM users WHERE is_admin = 1;
SELECT * FROM users WHERE is_admin > 0;
### Delete Row
###### DELETE FROM users WHERE id = 6;
### Update Row
###### UPDATE users SET email = 'freddy@gmail.com' WHERE id = 2;
### Add New Column
###### ALTER TABLE users ADD age VARCHAR(3);



##### 2-Practice running common SQL commands using the following SQL Bolt tutorials.

![](../assest/practice/1.png)
![](../assest/practice/2.png)Lessons 3
![](../assest/practice/3.png)Lessons 4
![](../assest/practice/4.png)Lessons 5
![](../assest/practice/5.png)Lessons 6
![](../assest/practice/6.png)Lessons 7
![](../assest/practice/7.png)Lessons 8
![](../assest/practice/8.png)Lessons 9
![](../assest/practice/9.png)Lessons 10
![](../assest/practice/10.png)Lessons 11
![](../assest/practice/11.png)Lessons 12
![](../assest/practice/12.png)Lessons 13
![](../assest/practice/13.png) Lessons 14
![](../assest/practice/14.png)Lessons 15
![](../assest/practice/15.png)Lessons 16
![](../assest/practice/16.png)Lessons 17
![](../assest/practice/17.png)Lessons 18
![](../assest/practice/18.png)
