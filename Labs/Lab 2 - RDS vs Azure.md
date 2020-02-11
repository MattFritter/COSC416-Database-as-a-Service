# COSC 416: Topics in Databases
# Winter 2020 - Lab 2

In this lab, we'll re-use some of what we learned from working with Amazon AWS in order to work with an Azure MySQL instance. We'll use Python to connect to RDS and Azure at the same time, and pit the two DBaaS options against each other.

## Table of Contents
- [Setting Up Our Instances](#instances)
- [Database Setup](#setup)
- [Generating Queries](#generate)
- [Timing our Queries](#timing)
- [Submission](#submission)

<a name="instances"></a>
## Setting Up Our Instances

For this lab, we'll need both an Amazon RDS database instance, as well as a Microsoft Azure Database for MySQL instance. See previous slides and labs for instructions on setting up your RDS instance, ensuring that it:

* Is publicly accessible
* Has inbound/outbound traffic allowed
* Is of the t2.micro instance type
* Is running MySQL

Refer to the Topic 9 notes for how to create an Azure database instance. You should make sure you complete the following steps:

* Enable access to your instance and add a rule to your firewall
* Are using the correct product (Azure Database for MySQL)
* Is a basic instance with 1 vCore

Once both instances are up and running, you should verify that you can connect to them, either from a Python script, or using a tool like HeidiSQL or SQuirreL. Make sure that you are able to connect before continuing on with the lab.

<a name="setup"></a>
## Database Setup (6 marks)

For our test, we'll want to make sure that both instances are using an identical database - to ensure fairness. Run the following SQL commands on both your instance (RDS and Azure), so that they both have a database called ```lab2db```, containing a table called ```testTable```:

```
CREATE DATABASE lab2db;
USE lab2db;
CREATE TABLE testTable (
	id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
	field1 VARCHAR(50),
	field2 VARCHAR(50),
	reg_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
	)
```

Use HeidiSQL or a MySQL prompt to confirm that your database was successfully created, and contains the testTable table.

<a name="generate"></a>
## Generating Queries (6 marks)

Now, we'll need to generate some queries for our databases. First, we'll need some imports:

```
import mysql.connector
import random
import string
```

We'll also want to define a special function called ```generateString```. When we make a call to this function, it will return a randomly-generated string, 50 characters long:

```
def generateString():
    letters = string.ascii_lowercase
    return ''.join(random.choice(letters) for i in range(50))
```

You'll need to create a ```mysql``` connection the same way as you did in the previous lab, using the ```mysql.connector.connect``` function to create a new connection object.

Once you've gotten a connection to your database instance, make sure that you switch to the correct database using your database cursor:

```
mycursor.execute("USE lab2db;")
```

Then, we'll be ready to start creating some insertions. We will generate a random string to go in the ```field1``` column, and will do this 250 times using a loop. We'll also want to save the randomly-generated values for ```field1```, so that we can later reference them for performing queries or updates.

```
entries1 = []	
for x in range(250):
    randString = generateString()
    entries1.append(randString)
    mycursor.execute("INSERT INTO testTable (field1, field2) VALUES (%s, %s)", (randString, "A"))
    mydb.commit()
```

Don't forget to ```commit()```. You should commit inside of the loop, rather than commit outside after all the statement executions are done.

Now, let's try doing the same, but performing an update. We'll need to randomly pick one of the entries and use that as our WHERE clause when performing the update:

```
for x in range(250):
    randEntry = random.choice(entries1)
    mycursor.execute("UPDATE testTable SET field2 = 'B' WHERE field1 = %s", (randEntry))
    mydb.commit()
```

Once you have inserts and updates working, **write your own code based on them that performs 250 deletions, and 250 queries, based on random data (could be the ID column, or field1/field2).** When you are done, you should have four loops: one for create, one for update, one for queries, and one for deletions.

Verify they all work by running your script in pieces. Double check that your script reliably writes to the database by logging in with MySQL terminal or HeidiSQL and verifying your creations/updates/queries and deletes are working properly.

<a name="timing"></a>
## Timing our Queries

Now, we'll need to time our queries. We can get the time it takes for code to run in Python using code such as the following:

```
import time

start = time.time()
# Code loop performing queries here
end = time.time()
totalTime = end - start
```

This will return the total time elapsed between the ```start``` and the ```end``` in seconds. Use this code wrapped around each of your loops to get the total time each for 250 Inserts, 250 Updates, 250 Queries, and 250 Deletions.

**Collect times for both your RDS instance and your Azure instance. Put these times into a text file called ```times.txt```.

<a name="submission"></a>
## Submission

Once you've completed your Python script, submit the script and your ```times.txt``` file into Moodle to complete the lab.