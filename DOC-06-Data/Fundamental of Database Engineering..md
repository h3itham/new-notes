#### Database phases
1. Planning OR requirement gathering.
2. Conceptual Design (ER Diagram). 
3. Logical Design (Schema)
4. Physical Design OR implementaion.
#### Module_1 "ACID" 
 ACID is a set of four key properties that guarantee database transactions are processed reliably. but first we should ask <span style='color:red'> What is the meaning of Transaction?</span> 
   Set of sequence of operations ( Read, Write or update ), Treated as single unit. 
- Transactional lifecycle 
	1. *BEGIN*: The transaction start. 
	2. *EXECUTE*: Database perform Operation(Read, Write)
	3. *COMMIT* OR *ROLLBACK*
		- Commit of all operation are success. 
		- Rollback if operation failed. 

- Example 
  I want to Transfer 100$ from account number 1 to account number 2 
	![[transaction-example.png]] 

	![[transaction.png]]

##### ACID 
-  **Atomicity:** 
	- Transaction consist from group of queries. 
	- Transactional is treated as single unit of work.  <span style='color:cyan'>(Atom can't be split)</span> 
	- All quires should be succeed
	- If one query fails, All prior successful queries should be rollback. 

- **Consistency:** Availability of correct information at the right time. 
- **Isolation:** Changes made in transaction are not visible to anther transaction until it is commited. 
- **Durability:** Once transaction Commit, the transaction Permanently saved, even in system failure. 

- Isolation levels
	1. Phantom Reads
		type of anomaly in databases that occurs when a transaction reads a set of rows matching a condition, and another transaction inserts or deletes rows that would have matched that condition **before the first transaction finishes**.
##### ACID LAB
1. Spin up postgres instance using docker. 
	```bash
	docker run -itd --name postgres-acid \
	  -e POSTGRES_USER=haitham \
	  -e POSTGRES_PASSWORD=haitham \
	  -e POSTGRES_DB=acid_test_db \
	  -p 5432:5432 \
	  -d postgres:16
	```
2. Connect to it 
	```bash
	docker exec -it postgres-acid psql -U haitham -d acid_test_db
	```
3. Create Table 
	```bash 
	CREATE TABLE accounts (
	    id SERIAL PRIMARY KEY,
	    name TEXT NOT NULL,
	    balance INT NOT NULL
	);
	
	INSERT INTO accounts (name, balance) VALUES 
	('Alice', 1000),
	('Bob', 1000);
	```
4. test Atomicity 
	1. Success Case 
		```sql
		BEGIN;
		UPDATE accounts SET balance = balance - 200 WHERE name = 'Alice';
		UPDATE accounts SET balance = balance + 200 WHERE name = 'Bob';
		COMMIT;
		```
	2. Failure case 
		```sql
		BEGIN;
		UPDATE accounts SET balance = balance - 200 WHERE name = 'Alice';
		-- Simulate a failure
		SELECT 1 / 0;
		ROLLBACK;
		```
5. Test Isolation 
	- Goal: One transaction doesn’t affect another until committed.
	- Open two terminals 
		```bash
		docker exec -it postgres-acid psql -U haitham -d acid_test_db
		```
	- Terminal "1"
		```sql 
		BEGIN;
		UPDATE accounts SET balance = balance - 100 WHERE name = 'Alice';
		-- don't commit yet
		```
	- Terminal "2"
		```sql 
		SELECT * FROM accounts;
		```
	

#### Module_2 "Understand Database Internals" 
**Storage Concepts**
1. *Table*: structure format for storing data in rows and columns. It is the primary structure for organizing and managing data. 
2. *Row_id*: Unique Identifier for Raw in table, it is internal and system maintained
   It's name in postgress `tuple_id` 
  ![](raw_id.png)
  3. *Page*: is the smallest unit of data storage in database. 
	  - All Rows stored and read in logical pages. 
	  - The database doesn't read a single row, it <span style="color:rgb(255, 0, 0)">reads a page </span>or more in a single IO. 
	  - Critical in database performance optimization. 
	  - Each Page has size (8KB in postgres and 16KB in mysql)
			![[pages.png| 150]]
4. IO: stands for `input/outpu`, Refer to process of reading and writing from the disk. 
	- We try to minimize this as much as possible. 


5. *Heap*: Is data structure where the tables is stored will all its pages one after another. 
	  - Traversing the heap is expensive as we need to read so many data and find what we want <span style='color:cyan'>So we need (indexes)</span>
		![[page-heap.png]]
	  

5. *Index*: is data structure which contain **Pointers** 
     - Improve the speed of data retrieval Operations on a database  
     - The smaller the index, the more it can fit in memory the faster the search. 
     - Update takes more time, SELECT take less time. <span style='color:red'>why?</span> 
       ![](indexing.png)
       
- what is the difference between `Raw Store` and `Column Store`. 
	![[column-raw.png]]


#### Module 4 "Database Indexing"
عبارة عن MetaData بتسهل عملية الوصول لل data. 
**Index** is data structure which contain pointers. 
You can review postgress from here [[Postgres Crash Course]]

1. Create Progress tables with millions Rows 
	- Create Postgres container 
	  ```bash
	  docker run -itd --name postgress -p 5432:5432 -e POSTGRES_PASSWORD=haitham postgres
	   ```
	- Postgres shell 
	   ```bash 
	   docker exec -it postgress psql -U postgres
		```
	- Create database I createit from GUI 
	- switch to it 
	  ```bash
	   \c indexing 
	   ```
	  
	- Create table 
	   ```bash
	    create table temp( t int);
		```
	- Insert values into tables. 
	  ```bash
	    insert into temp(t) select random() *100  from generate_series(0, 1000000); 
		```
	- For validation 
		```bash 
		select t from temp limit 10; 
		```
	- Create another table which have string data on it 
		```sql 
		CREATE TABLE employees (
		    id SERIAL PRIMARY KEY,
		    name VARCHAR(20)
		);
		```
	- Insert data in this table 
		```sql 
		INSERT INTO employees (name)
		SELECT 'employee_' || i
		FROM generate_series(1, 1000000) AS s(i);
		```

Our Development will be on `employees` table. This table contain two columns one for `id`and the other for `employee`. the `id` column have index by default but the `employee` hasn't. 

Analyze the different between the next queries 
- For id 
	```sql 
	explain analyze select id from employees where id=1000; 
	```
- For `employee`
	```sql 
	explain analyze select name from employees where id=1100; 
	```
- Find id for employe `employee_500000`
	```sql 
	EXPLAIN ANALYZE SELECT id FROM employees WHERE name = 'employee_500000';
	```
	This will take so much time, Because there is no index for `employee` column.  
- To solve this we will create an index for `name` column 
	```sql 
	create index employees_name on employees(name);  
	```
	This will take slight time to create entire index. to check if all is ok rerun the previous query 
	```bash 
	explain analyze select id from employees where name = 'employee_550000'; 
	```
##### Indexing In MySQL 
- Index is database instruction is that improve database retrieval. 
- Disadvantages 
	1. Consume more storage. 
	2. Slow Writing process (INSERT, UPDATE, and DELETE). 


- MySQL Example 
	- Create MySQL docker instance 
		```bash 
		docker run --name mysql-test -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -d mysql:8
		```
	- Enter MySQL shell 
		```bash 
		docker exec -it mysql-test bash 
		mysql -u root -proot 
		```
	- Create Database and table 
		```sql 
		CREATE DATABASE test_db;
		USE test_db;
		
		CREATE TABLE users (
		    id INT AUTO_INCREMENT PRIMARY KEY,
		    name VARCHAR(255),
		    email VARCHAR(255),
		    age INT
		);
		
		```
	- Generate 3 billion of records 
		```sql 
		DELIMITER $$
		
		CREATE PROCEDURE InsertUsers()
		BEGIN
		    DECLARE i INT DEFAULT 0;
		    WHILE i < 1000000 DO
		        INSERT INTO users (name, email, age)
		        VALUES (
		            CONCAT('User', i),
		            CONCAT('user', i, '@example.com'),
		            FLOOR(18 + (RAND() * 50))
		        );
		        SET i = i + 1;
		    END WHILE;
		END$$
		
		DELIMITER ;
		
		CALL InsertUsers();
		
		```
	- Test Query Speed without index  
		```sql 
		EXPLAIN SELECT * FROM users WHERE email = 'user1000010@example.com';
		```
	- Create Index 
		```sql 
		CREATE INDEX idx_email ON users(email);
		```
	- Test Query speep with index 
		```sql 
		CREATE INDEX idx_email ON users(email);
		```

#### Slow query 
- Activate Slow query on MySQL 
	```sql 
	SET GLOBAL slow_query_log = 'ON';
	SET GLOBAL long_query_time = 1;  -- Log queries that take more than 1 second
	SET GLOBAL log_output = 'FILE';  -- Save logs to a file
	SET GLOBAL slow_query_log_file = '/var/lib/mysql/slow.log';  -- Log file location
	```
- To summarize slow queries use `mysqldumpslow` tool 
	```bash
	mysqldumpslow -a /var/lib/mysql/slow.log
	mysqldumpslow -s t -t 10 /var/lib/mysql/slow.log
	```

#####  Query Optimizer and and query planner 
   Are critical components responsible for determine the most effective way to execute SQL Query. 

**Optimizer**
- Determine the best way to execute query. 
- Steps 
	1. Parse Query
	2. Generate Execution Plans 
	3. Cost Estimation 
	4. Choose the best Plan. 
**Planner** 
- Take plan from Optimizer and Translate into series of steps that the database. 

#### Bitmap Index scan vs Index scan vs table scan 
- Bitmap: s a compact data structure that uses **bits (0 or 1)** to represent the presence (1) or absence (0) of a value — like a map of matching rows.
	Example 
	If a table has 5 rows, and you're searching for `status` = `active`, a bitmap index might look like 
	```txt 
	Row ID:   1  2  3  4  5  
	Bitmap:   1  0  1  0  1   → match found in rows 1, 3, and 5
	```
- Comparison 

| Type                      | Description                                                     | When It's Used                                                                                 | Pros                                                  | Cons                                             |
| ------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------ |
| **Bitmap Index Scan**     | Uses a **bitmap** to combine multiple index results efficiently | Best for **low-cardinality** columns (few unique values like `male/female`, `active/inactive`) | Fast for complex filters                              | Slower on updates/inserts                        |
| **Index Scan**            | Walks through the B-Tree index and fetches matching rows        | When filtering on **indexed column** with **many unique values**                               | Very fast on **high-cardinality** data (e.g., emails) | Not great for combining multiple conditions      |
| **Table Scan (Seq Scan)** | Reads the entire table row by row                               | When **no index exists**, or **most rows are needed**                                          | Simple, fast if table is small                        | Very slow on large tables with few matching rows |
   

#### Key vs Non key column Database indexing. 
