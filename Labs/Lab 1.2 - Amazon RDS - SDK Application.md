# COSC 416: Topics in Databases
# Winter 2020 - Lab 1 Part 2

In this lab, we'll combine what we've learnt about CLI/SDK programming with Amazon RDS to create a basic application that will allow us to sandbox a database for temporary use.

## Table of Contents
- [The Basic Idea](#idea)
- [Using the SDK - Creating the Instance](#begin)
- [Connecting to SQL](#connect)
- [Deleting the Instance](#delete)

<a name="idea"></a>
## The Basic Idea

Our application will perform the following tasks:

1. Create a database instance
2. Establish an SQL connection to that instance
3. Allow the user to enter SQL statements, which are then executed
4. Delete the database instance

A couple things will need to be taken into account. First, we'll need two libraries: the Amazon AWS SDK for your choice of programming language (Examples will be given in Python), and a mysql connector. The SDK will allow us to directly create and destroy database instances in RDS, while the mysql connector will allow us to connect to our instance and execute SQL on it.

We will also need to consider the time it takes to create and destroy a database. We cannot create an SQL connection to our database instance until it is completely built, and we don't want to close our application until we've confirmed that the instance has been deleted. So, we'll need some loops checking for database status and putting the application on hold until our creation/deletion is finished.

We will also need a loop in the middle, which prompts for SQL input from the user, executes it on the database, and then returns any results from the SQL. This loop will be terminated when the user enters the word ```exit```, prompting the application to begin deleting the database instance.

<img src="https://i.imgur.com/GSQWOUl.png">

<a name="begin"></a>
## Using the SDK - Creating the Instance (6 marks)

We'll begin our Python file (```*.py```) with our library imports. For Python, the libraries you will want to use are the Amazon AWS SDK library ```boto3```, instructions are available <a href="https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html">here</a>. The second library will will need is the ```mysql.connector``` library, which allows us to connect to MySQL databases (or MariaDB, as they are cross-compatible). Instructions for installing the mysql.connector library are available <a href="https://pynative.com/install-mysql-connector-python/">here</a>.

Once installed, we include the library imports. We'll also want to include the ```time``` library for handling delays between messages:

```
import boto3
import mysql.connector
import time
```

Before you've gone any further, make sure you have the Amazon AWS CLI installed and configured with your security credentials . Once we've done that, we'll create a client connection to the RDS service, and then prompt the user to provide a database instance name:

```
myRds = boto3.client('rds', region_name='*insert-region-here*')
dbname = input("Enter database name: ")
```

Make sure you use the correct region - it should be the same region you used for lab 1.1. Then, we'll make an SDK call to create a new database instance. We should use a ```try```/```except``` to make sure that catch any errors if the database instance with that name already exists, or can otherwise not be created:

```
try:
    myRds.create_db_instance(
        DBInstanceIdentifier=dbname,
        AllocatedStorage=10,
        Engine="mysql",
        DBInstanceClass="db.t2.micro",
        MasterUsername="root",
        MasterUserPassword="password",
        PubliclyAccessible=True
        )
except:
    print("DB instance create failed.")
```

Make sure you include the ```PubliclyAccessible``` flag, and specify a ```MasterUsername``` and ```MasterUserPassword``` of your own choice. You may wish to set these to variables, as you'll need them again when connecting via SQL.

Once we've begun creating the instance, we'll need to wait for the instance to be available before we can connect. You can perform a check using the following:

```
status = myRds.describe_db_instances(DBInstanceIdentifier=dbname)
currentStatus = status["DBInstances"][0]["DBInstanceStatus"]
```

This will fetch the ```DBInstanceStatus``` field for the created instance. When the instance is available and ready to use, ```currentStatus``` will have a string value equal to ```available```.

Create a loop that performs this check regularly, either printing out a message if the database is still being built, or printing out a success message and breaking out of the loop if the instance is available.

You can (and should) add a timer to your loop to add a delay between performing status checks. For example, the following line will cause the application to sleep for 30 seconds:

```
time.sleep(30)
```

It may take in upwards of 6-8 minutes for the instance to be completely created.

<a name="connect"></a>
## Connecting to SQL (6 marks)

Once we've created an instance and confirmed that it is now available, we can connect to it via SQL and begin passing it user SQL input. To begin, we'll need to establish our endpoint, the address where we will be able to reach our instance via the web. We can get this information from the status info we've already fetched previously:

```
address = status["DBInstances"][0]["Endpoint"]["Address"]
```

Then we can pass it, along with the username and password we used earlier to create our instance, to the ```mysql.connector``` connection instance to establish our database connection:

```
myDB = mysql.connector.connect(
    host=address,
    user="root",
    passwd="password"
)
```

If you get a connection error saying you cannot connect to the database instance, double check that the ```PubliclyAccessible``` flag is ```True``` in the database creation function, and double check that the region is correct - if you use the wrong region, it may use different default VPC security groups, which don't have traffic allowed through them (which we enabled in lab 1.1).

Once you've established the connection, we can create a database cursor, print out a message to the user, and prompt the user for SQL input:

```
mycursor = myDB.cursor()
print("SQL Terminal. Enter 'exit' to close.")
sqlInput = input(">>> ")
```

Now, we'll need to create a loop that performs the following actions, beginning with the execution of the SQL input:

```
mycursor.execute(sqlInput)
```

Then we'll want to fetch the results of our query. We'll need to put it in a ```try```/```except```, as some actions (such as creating a database) do not return any results from the cursor. We'll also perform a ```commit()```, which ensures that our writes are actually sent to the server when performing a change to the database, and print out a string representation of the query output:

```
try:
	results = mycursor.fetchall()
	myDB.commit()
	print(str(results))
except:
	print("No Output.")
```

Your loop should continue infinitely, prompting the user for input, outputting the results, and then re-prompting for more input until the user enters the word ```exit```, at which point the loop breaks. An example of this type of loop declaration is given below:

```
while str(sqlInput) != "exit":
```

Once we've exited the loop, we'll print out a message to the user to let them know they are exiting, and we'll close down the SQL connection:

```
myDB.close()
print("Goodbye. Deleting database...")
```

<a name="delete"></a>
## Deleting the Instance (3 marks)

Once the user has exited the SQL terminal, we want to delete the database instance that we created. We can do this easily using an SDK command, just as we did for creating the instance:

```
try:
    myRds.delete_db_instance(
		DBInstanceIdentifier=dbname,
		SkipFinalSnapshot=True,
		DeleteAutomatedBackups=True
    )
except:
    print("DB instance deletion failure.")
```

You will want to make sure that ```SkipFinalSnapshot``` is ```True```, to prevent the system from creating an automatic backup snapshot of the database before deletion. We will also want to make sure that tell RDS to delete any automatic backups of the database using the ```DeleteAutomatedBackups``` tag.

Now, we'll wait for the database to be fully deleted before we exit the application. This is similar to the loop used when waiting for the instance to be created. However, instead of checking for a ```DBInstanceStatus```, we will watch for an exception when we can no longer get the status (because the instance no longer exists).

We use the ```describe_db_instances()``` function again in a ```try```/```except```, and when we encounter an exception, we're done:

```
try:
    myRds.describe_db_instances(DBInstanceIdentifier=dbname)
	print("Waiting for database to delete...")
except myRds.exceptions.DBInstanceNotFoundFault:
	print("Database deleted. Exiting.")
	break
```

This loop should also contain a ```time.sleep()``` call as your first loop did, so that you aren't constantly making SDK calls - rather, you make the SDK calls every 30 or 60 seconds.

<a name="final"></a>
## Final Feature (2 marks)

As a final component to this lab, let's improve our database application, by implementing **one** feature from the following list:

* Prompt the user to choose an engine type, either MySQL or MariaDB. Both are compatible with the mysql.connector and should otherwise work fine.
* Ask the user if they want to delete the database, otherwise allow them to exit without deleting the database instance.
* Provide some sort of logging function, which writes the user SQL input and the resultant output to an external text file.

It is up to you to decide how you want to implement the feature of your choice. Test your software to make sure it works. For faster testing, you may want to disable the deletion of your database instance, so you can skip the creation/deletion time required each time you run your script.

<a name="Submission"></a>
## Submission

Once you've completed your Python script, submit it via Moodle. You will have two weeks to work on this lab.