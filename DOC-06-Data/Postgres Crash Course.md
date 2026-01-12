#### Introduction 
- PostgreSQL is an free open-source database system that supports both relational (SQL) and non-relational (JSON) queries.
- PostgreSQL is a back-end database for dynamic websites and web applications.
- PostgreSQL supports the most important programming language. 
- We will use docker container to learn postgres 
	```bash 
	docker run -itd --name deleteme -e POSTGRES_USER=root -e POSTGRES_PASSWORD=root -p 5432:5432 postgres
	```
#### Connect to postgres
There is a lot of command line tools which are used to connect and control postgres database, one of the most popular tool is `psql`
- Installation (ubuntu)
   ```bash
     sudo apt install -y postgresql-client 
     ```
 - Connect to any postgres database 
   ```bash 
     psql -U [username] -h [hostname/IP] -d [databasename]
	 ```
	 
- Basic command in psql 
	1. List all databases
	   ```bash 
	     \l
		```
	2. connect to databases
	   ```bash 
	    \c [database_name]
		```
	3. List all tables 
	   ```bash 
	     \dt 
		```
	4. Describe table 
	   ```bash 
	    \d [table_name]
		```
	5. Exit 
	   ```bash
	     \q 
        ``` 

#### Database Management 
1. Create Database 
   ```sql
     CREATE DATABASE dbname;
     ```
 2. Drop Database
    ```sql
    DROP DATABASE dbname;
    ```
#### User Management 
1. create user 
   ```sql
     CREATE USER username WITH PASSWORD 'password';
     ```
 2. Grant Privileges to a users
    ```sql
    GRANT ALL PRIVILEGES ON DATABASE dbname TO username;
    ```
3. Revoke privileges 
   ```sql 
     REVOKE ALL PRIVILEGES ON DATABASE dbname FROM username;
     ```


#### Tables 
1. Create table 
   ```sql
     CREATE TABLE employees (
		    id SERIAL PRIMARY KEY,
		    name VARCHAR(50),
		    position VARCHAR(50),
		    salary NUMERIC
		);

     ```

2. Insert Data 
   ```sql 
     INSERT INTO employees (
       name, position, salary)
       VALUES ('John Doe', 'Manager', 5000);
	```

3. Update Data 
   ```sql
     UPDATE employees SET salary = 5500 WHERE name = 'John Doe';
	```

4. Delete Data 
   ```sql 
     DELETE FROM employees WHERE name = 'John Doe';
     ```
 5. Drop Table 
    ```sql 
    DROP TABLE employees;
	``` 

6. Describe table 
	```sql 
	\dt 
	```


##### Extensions 
- is a piece of software that increase database functionality. 
- For monitor performance I'll use `pg_stat_statements`
	- Track execution statistics of all SQL statement. 
	- Helps find slow queries and high-load operations. 