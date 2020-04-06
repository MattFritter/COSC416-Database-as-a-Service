# COSC 416: Topics in Databases
# Winter 2020 - Lab 2.1

In this lab, we'll update our Lab 2 code to instead perform a comparison between an Azure MySQL database and an Azure PostgreSQL database.

## Table of Contents
- [Instance Setup](#instances)
- [Database Setup](#setup)
- [Modifying Our Code](#generate)
- [Submission](#submission)

<a name="instances"></a>
## Instance Setup (1 mark)

For this lab, we'll need one Azure Database for MySQL instance, and one Azure Database for PostgreSQL instance. Make sure that the security settings for both of these instances:

* Have connections enabled
* Whitelisted for your IP
* Have SSL disabled

You can refer back to the Topic 9 and Topic 10 notes if you require assistance with these steps. You may wish to use software like HeidiSQL or an equivalent to make sure that your databases are set up properly.

<a name="setup"></a>
## Database Setup (2 marks)

We'll need to use a slightly different table setup for our PostgreSQL table, as PostgreSQL uses slightly different table declarations. Below is the standard **MySQL** table you should use for the MySQL database.

```
CREATE TABLE testTable (
	id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
	field1 VARCHAR(50),
	field2 VARCHAR(50),
	reg_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
	)
```

And here is the creation code for the PostgreSQL table:

```
CREATE TABLE testTable (
	id SERIAL,
	field1 VARCHAR(50),
	field2 VARCHAR(50),
	reg_date TIMESTAMP DEFAULT now()
	)
```

You will need to create a database first in MySQL. For the PostgreSQL server, you can use the default ```postgres``` database for this lab. Remember, you'll need to specify which database you are using when you connect to PostgreSQL.

<a name="generate"></a>
## Modifying Our Code (3 marks)

Your lab 2 code should produce 250 each of inserts, updates, deletions, and queries. Modify your existing code to run the test first on your MySQL instance, and then on your PostgreSQL instance. You shouldn't have to make any major changes to the queries themselves.

You will need to install the ```psycopg2``` library in Python (use ```pip install psycopg2```) in order to connect to your PostgreSQL instance. An example of creating this connection is provided below (fill in the specified data):

```
import psycopg2

mydb = psycopg2.connect(
  host="<My database instance>",
  user="<username>@<instancename>",
  password="<password>",
  database="postgres"
)
```

Create a ```times.txt``` for both the MySQL and the PostgreSQL instances. At the bottom of your text file, discuss which of the two databases performed better based on the results you got in testing.

<a name="submission"></a>
## Submission

Once you've completed your Python script, submit your edited script and your ```times.txt``` file on Moodle to complete the lab.