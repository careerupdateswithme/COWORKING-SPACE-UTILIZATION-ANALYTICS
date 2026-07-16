-- ==========================================================
-- PROJECT DESCRIPTION
-- COWORKING SPACE UTILIZATION ANALYTICS USING MYSQL
-- ==========================================================

-- Developed a MySQL database solution for WorkNest Spaces Pvt. Ltd.
-- to help the Workspace Performance Analytics Department analyze
-- workspace bookings, member activity, branch utilization,
-- and revenue trends. Designed relational tables, maintained
-- booking records, and generated business reports to support
-- management decision-making.

-- COMPANY OVERVIEW {{WorkNest Spaces Pvt. Ltd.}}

-- WorkNest Spaces Pvt. Ltd. provides coworking offices,
-- meeting rooms, private cabins, and hot desks for startups,
-- freelancers, and businesses across multiple cities in India.

-- DEPARTMENT
-- Workspace Performance Analytics

-- EMPLOYEE ASSIGNED

-- Employee Name : Aditya Joshi
-- Designation   : Business Intelligence Trainee
-- Employee ID   : EMP055
-- Department    : Workspace Performance Analytics

-- BUSINESS PROBLEM ::

-- WorkNest manages multiple coworking branches and hundreds
-- of bookings every month. Management wants answers to:

-- Which branches are most utilized?
-- Which members generate the highest revenue?
-- Which workspace types are most popular?
-- What are the monthly booking trends?
-- Which city contributes the highest revenue?

-- Existing spreadsheet-based tracking created reporting delays
-- and made business analysis difficult.

-- SOLUTION ::

-- Aditya Joshi has been assigned to develop a MySQL database
-- solution to store member, branch, workspace, booking,
-- and payment information and generate analytical reports
-- for management.

-- ==========================================================
-- DATABASE
-- ==========================================================

CREATE DATABASE worknest_coworking_db;
USE worknest_coworking_db;

-- TABLE 1 : MEMBERS

CREATE TABLE members(
member_id INT PRIMARY KEY,
member_name VARCHAR(100),
city VARCHAR(50)
);

INSERT INTO members VALUES
(1,'Amit Shah','Mumbai'),
(2,'Neha Patil','Pune'),
(3,'Rahul Verma','Bangalore'),
(4,'Priya Singh','Delhi'),
(5,'Arjun Mehta','Hyderabad'),
(6,'Sneha Joshi','Mumbai'),
(7,'Vikram Rao','Ahmedabad'),
(8,'Riya Gupta','Pune'),
(9,'Karan Patel','Chennai'),
(10,'Ananya Kulkarni','Bangalore');

-- TABLE 2 : BRANCHES

CREATE TABLE branches(
branch_id INT PRIMARY KEY,
branch_name VARCHAR(100),
city VARCHAR(50)
);

INSERT INTO branches VALUES
(101,'WorkNest Mumbai','Mumbai'),
(102,'WorkNest Pune','Pune'),
(103,'WorkNest Bangalore','Bangalore'),
(104,'WorkNest Delhi','Delhi'),
(105,'WorkNest Hyderabad','Hyderabad'),
(106,'WorkNest Chennai','Chennai'),
(107,'WorkNest Ahmedabad','Ahmedabad'),
(108,'WorkNest Jaipur','Jaipur'),
(109,'WorkNest Kolkata','Kolkata'),
(110,'WorkNest Surat','Surat');

-- TABLE 3 : WORKSPACES

CREATE TABLE workspaces(
workspace_id INT PRIMARY KEY,
workspace_type VARCHAR(50),
daily_rate DECIMAL(10,2)
);

INSERT INTO workspaces VALUES
(201,'Hot Desk',500),
(202,'Private Cabin',1200),
(203,'Meeting Room',1500),
(204,'Hot Desk',500),
(205,'Private Cabin',1200),
(206,'Meeting Room',1500),
(207,'Hot Desk',500),
(208,'Private Cabin',1200),
(209,'Meeting Room',1500),
(210,'Hot Desk',500);

-- TABLE 4 : BOOKINGS

CREATE TABLE bookings(
booking_id INT PRIMARY KEY,
member_id INT,
branch_id INT,
workspace_id INT,
days_booked INT,
booking_date DATE,
FOREIGN KEY(member_id) REFERENCES members(member_id),
FOREIGN KEY(branch_id) REFERENCES branches(branch_id),
FOREIGN KEY(workspace_id) REFERENCES workspaces(workspace_id)
);

INSERT INTO bookings VALUES
(1001,1,101,201,5,'2025-01-05'),
(1002,2,102,202,3,'2025-01-08'),
(1003,3,103,203,2,'2025-01-10'),
(1004,4,104,204,6,'2025-01-12'),
(1005,5,105,205,4,'2025-01-15'),
(1006,6,106,206,2,'2025-02-01'),
(1007,7,107,207,7,'2025-02-05'),
(1008,8,108,208,3,'2025-02-08'),
(1009,9,109,209,5,'2025-02-12'),
(1010,10,110,210,6,'2025-02-15');

-- TABLE 5 : PAYMENTS

CREATE TABLE payments(
payment_id INT PRIMARY KEY,
booking_id INT,
amount DECIMAL(10,2),
FOREIGN KEY(booking_id) REFERENCES bookings(booking_id)
);

INSERT INTO payments VALUES
(1,1001,2500),
(2,1002,3600),
(3,1003,3000),
(4,1004,3000),
(5,1005,4800),
(6,1006,3000),
(7,1007,3500),
(8,1008,3600),
(9,1009,7500),
(10,1010,3000);

-- ==========================================================
-- SQL CHALLENGES
-- ==========================================================

-- 1. Display all members
SELECT * FROM members;

-- 2. Display all branches
SELECT * FROM branches;

-- 3. Display all workspaces
SELECT * FROM workspaces;

-- 4. Display all bookings
SELECT * FROM bookings;

-- 5. Display all payments
SELECT * FROM payments;

-- 6. Count total members
SELECT COUNT(*) AS total_members
FROM members;

-- 7. Count total branches
SELECT COUNT(*) AS total_branches
FROM branches;

-- 8. Calculate total booking days
SELECT SUM(days_booked) AS total_booking_days
FROM bookings;

-- 9. Find maximum booking days
SELECT MAX(days_booked) AS max_days
FROM bookings;

-- 10. Count members city-wise
SELECT city,COUNT(*) AS total_members
FROM members
GROUP BY city;

-- 11. Calculate total company revenue
SELECT SUM(amount) AS total_revenue
FROM payments;

-- 12. Generate member-wise revenue report
SELECT m.member_name,
SUM(p.amount) AS revenue
FROM members m
JOIN bookings b ON m.member_id=b.member_id
JOIN payments p ON b.booking_id=p.booking_id
GROUP BY m.member_name;

-- 13. Find highest paying member
SELECT m.member_name,
SUM(p.amount) AS spending
FROM members m
JOIN bookings b ON m.member_id=b.member_id
JOIN payments p ON b.booking_id=p.booking_id
GROUP BY m.member_name
ORDER BY spending DESC
LIMIT 1;

-- 14. Generate branch-wise revenue report
SELECT br.branch_name,
SUM(p.amount) AS revenue
FROM branches br
JOIN bookings b ON br.branch_id=b.branch_id
JOIN payments p ON b.booking_id=p.booking_id
GROUP BY br.branch_name;

-- 15. Find most utilized branch
SELECT br.branch_name,
SUM(b.days_booked) AS utilization
FROM branches br
JOIN bookings b ON br.branch_id=b.branch_id
GROUP BY br.branch_name
ORDER BY utilization DESC
LIMIT 1;

-- 16. Generate workspace type usage report
SELECT w.workspace_type,
COUNT(*) AS total_bookings
FROM workspaces w
JOIN bookings b ON w.workspace_id=b.workspace_id
GROUP BY w.workspace_type;

-- 17. Calculate average booking revenue
SELECT AVG(amount) AS average_revenue
FROM payments;

-- 18. Find top 3 members by spending
SELECT m.member_name,
SUM(p.amount) AS spending
FROM members m
JOIN bookings b ON m.member_id=b.member_id
JOIN payments p ON b.booking_id=p.booking_id
GROUP BY m.member_name
ORDER BY spending DESC
LIMIT 3;

-- 19. Generate city-wise branch report
SELECT city,
COUNT(*) AS total_branches
FROM branches
GROUP BY city;

-- 20. Generate branch utilization report
SELECT branch_id,
COUNT(*) AS total_bookings,
SUM(days_booked) AS total_days
FROM bookings
GROUP BY branch_id;
