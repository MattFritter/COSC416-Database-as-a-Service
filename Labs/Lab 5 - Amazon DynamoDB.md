# COSC 416: Topics in Databases
# Winter 2020 - Lab 5

In this lab, we'll explore some of the functionality of Amazon DynamoDB, and we'll take a look at how we can use DynamoDB

## Table of Contents
- [Creating a DynamoDB Instance](#install)
- [Filtering Your Results](#filter)
- [Setting Up a Local DynamoDB Server](#local)
- [Submission](#sub)

<a name="install"></a>
## Creating a DynamoDB Instance (3 marks)

To begin, follow the instructions from the Topic 15 notes to create a new DynamoDB database, using the Amazon Web GUI. Provide your table with a name, and then insert at least *10* different items within the table.

Your table and items should make use of both a partition key, and a sort key - meaning some of your items should have the same partition key value. In addition, your items should have some attributes (i.e. fields) that are shared across multiple items, and some attributes that exist only for one or two items. You should use a mix of attribute types (strings, arrays, numbers, etc).

To generate data for your items, it may be helpful to use a random JSON generator, such as the one available <a href="https://www.json-generator.com">here</a>.

*Once you have created your table and added your items, take a screenshot of your DynamoDB dashboard showing the items in the table.*

<a name="filter"></a>
## Filtering Your Results (4 marks)

Now that the table has been created and populated with items, we will try performing some basic filtering. For each item below, come up with filter settings in the Web GUI and then *screenshot them* and the results of the filter:

1. Provide a filter that selects *one* item based on a specific attribute value (i.e. SELECT * WHERE name = "Matthew Fritter"; in SQL syntax)
2. Provide a filter that selects all items that are *missing* a given attribute. (i.e. SELECT * WHERE NOT EXISTS myAttribute;)
3. Provide a filter that selects all items that share a given partition key value (refer to Topic 15 slides for assistance with this).

<a name="local"></a>
## Setting Up a Local DynamoDB Server (2 marks)

Following the instructions from the Topic 16 notes, try installing and running a localhosted version of DynamoDB. You should already have the Amazon CLI installed from our previous experience with Amazon AWS, so try using the CLI to connect to your locally hosted DynamoDB server. Remember to use PowerShell if you are on Windows, as the standard command prompt will cause problems when dealing with JSON data.

First, create a table in your locally hosted DynamoDB instance using the command format described <a href="https://docs.aws.amazon.com/cli/latest/reference/dynamodb/create-table.html">here</a>. Then, using the format described <a href="https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html">here</a>, try inserting one of the items you came up with for the previous section into your locally hosted DynamoDB instance using the CLI. Note this will be rather tricky, and requires some precise formatting - pick a smaller item to make it easier.

Once you've managed to insert an item into your table using ```put-item```, use the example code on slide 20 of the Topic 16 notes to print a full scan of your locally hosted table, so that your output looks similar to the output on slide 22 of the Topic 16 notes (except a ```scan```, not a ```get-item```). *Take a screenshot of the output in your terminal/PowerShell*.

<a name="sub"></a>
## Submission

Upload the five requested screenshots on Moodle to complete the lab submission.
