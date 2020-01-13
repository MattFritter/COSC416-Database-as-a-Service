# COSC 416: Topics in Databases
# Winter 2020 - Lab 1 Part 1

Today, we'll begin getting our Amazon RDS instance set up and ready to go for our first project. We'll also do some basic testing to make sure we can actually communicate with our RDS instance.

## Table of Contents
- [Creating your RDS Instance](#install)
- [Connecting to your RDS Database](#conn)
- [Creating Database Snapshots](#snapshots)
- [Submission](#sub)

<a name="install"></a>
## Creating your RDS Instance (2 marks)

Using the Topic 3 lecture slides, follow along and create an Amazon RDS/AWS account. Once you've done that, create your first *database instance*. Your database instance should have the following properties:

* Use the db.t2.micro instance class
* Use the MySQL database engine (default version is fine)
* Publicly accessible
* A master username and password of your choice
* (Optional) Enhanced Logging/Statistics enabled

Once you've created your instance, move along with the notes and create a new inbound traffic rule for the default security group. Allow *all* traffic into your server from any IP address. Make sure you save your outbound rule.

<a name="conn"></a>
## Connecting to your RDS Database (1 mark)

Once you have created your instance, try connecting to your SQL server remotely. You may use the command line, an SQL software like HeidiSQL, or a custom program written in a programming language of your choice. You may choose to follow the example Python code provided in the lecture, as we will be working with similar code for the second part of this lab.

When you have successfully connected to your database instance, *take a screenshot.* This screenshot should clearly show that you have successfully connected to and can communicate with your RDS instance.

<a name="snapshots"></a>
## Creating Database Snapshots (2 mark)

Now, try making some changes to your database instance: you might try creating a new database, or a new table, or something along those lines. Then, take a snapshot of your database, and name the snapshot *lab1_snap*. *Take a screenshot of the snapshots page for your database instance, showing the snapshot you just created*.

Try changing your database again. If you wanted to go back to the way it was before, you could restore your snapshot onto a new instance. Try doing this - restore it so that you have a second instance reflecting the old version of the database. *Take a screenshot of the two instances in your RDS dashboard, and then delete the new instance you've created.*

<a name="sub"></a>
## Submission

Upload your three screenshots to the Moodle assignment page for this lab. Don't delete anything, as we'll be using this RDS instance for the duration of lab 1.