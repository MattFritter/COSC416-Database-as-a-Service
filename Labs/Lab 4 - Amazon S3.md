# COSC 416: Topics in Databases
# Winter 2020 - Lab 4

In this lab, we'll use the Simple Storage Service (S3) to create a static web page on demand using the Python **boto3** library.

## Table of Contents
- [Creating Your Webpage](#webpage)
- [Your Boto3 Script](#script)
- [Submission](#submission)

<a name="webpage"></a>
## Creating Your Webpage (1 mark + 1 Bonus)

First, you'll want to create your web page and the necessary resources. Your web page should have, at a minimum:

*An HTML file that is the main file for your web page
*A JavaScript file to give your page some element of interactivity
*A Cascading Style Sheet (CSS) file to style your page

Beyond these minimum requirements, the design and content of the web page is up to you. There is one bonus mark available for students who put at least some effort into their web pages. JavaScript and CSS jokes/quirks are welcome and appreciated. Entertaining pages will be more likely to get the bonus mark.

Keep in mind that references to your JavaScript and CSS files may need to be changed (or use local references) as the files in question will be served directly from S3.

<a name="script"></a>
## Your Boto3 Script (5 marks)

Now, we'll make a Python script file called ```buildsite.py``` that generates our website for us automatically. Before you begin, you'll need to make sure that you have the permissions set up correctly so that the ```boto3``` library has access to S3 - see the lecture slides for details.

The first thing we'll need to do is create a new bucket for our website. We can do this with the following code:

```
bucket_name = "myBucket"
region = {'LocationConstraint': "us-east-2"}
client.create_bucket(Bucket=bucket_name, CreateBucketConfiguration=region)
```

You should prompt the user to enter a name for the bucket (you can use the ```input()``` function for this), and you should wrap the whole thing in a try-except loop. If the user enters a bucket name that has already been taken, you'll need to prompt them for another name and try creating the bucket again.

We will need to make our bucket public if we wish to be able to serve objects from it publicly. We can change the permissions for the bucket by setting a new policy for the bucket (taken from the Amazon Documentation):

```
import json

# Create a bucket policy
bucket_name = 'BUCKET_NAME'
bucket_policy = {
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"PublicRead",
      "Effect":"Allow",
      "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::BUCKET_NAME/*"]
    }
  ]}

# Convert the policy from JSON dict to string
bucket_policy = json.dumps(bucket_policy)

# Set the new policy
s3 = boto3.client('s3')
s3.put_bucket_policy(Bucket=bucket_name, Policy=bucket_policy)
```

Once the bucket has been created and made public, we should automatically upload our website files into the bucket. See the lecture slides for how to do this. An example of how to do this programmatically is given below:

```
response = client.upload_file('time.js', 'cosc416test', 'time_new.js')
``` 

Make sure that you get the ordering of the arguments correct: the first argument is the local name of the file to upload (your HTML/JS/CSS files), the second argument is the bucket name (for the bucket we just created), and the third argument is the name (or key) of the file once it is uploaded.

You will also need to include the additional argument:

```
ExtraArgs={'ACL': 'public-read'}
```

In order to make your files publicly accessible once they've been uploaded (by default, they will not be publicly accessible).

Once you've completed the Python script, try visiting the URL for your web page at:

```
YourBucketName.s3.us-east-2.amazonaws.com/YourHtmlFile.html
```

Make sure that your HTML, CSS, and JS resources are all working correctly and are publicly accessible on S3.

<a name="submission"></a>
## Submission

Put your Python script and your web files (CSS/JS/HTML) into a ZIP archive or similar (7z, Tar, RAR are also acceptable), and upload on Moodle.