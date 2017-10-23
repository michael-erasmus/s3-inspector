<p align="center">
   <img alt="S3 Inspector" src="https://github.com/kromtech/s3-inspector/blob/logo/logo.png" width="400"/>
</p>

Tool to check AWS S3 bucket permissions

## What it does
 - Checks all your buckets for public access
 - For every bucket gives you the report with:
   - Indicator if your bucket is public or not
   - Permissions for your bucket if it is public
   - List of URLs to access your bucket (non-public buckets will return Access Denied) if it is public

## Prerequisites
 - **Create IAM user with AmazonS3ReadOnly policy attached**
   - Go to IAM (https://console.aws.amazon.com/iam/home)
   - Click "Users" on the left hand side menu
   - Click "Add user"
   - Fill in user name and check **Programmatic access**
   - Click "Next: Permissions"
   - Click "Attach existing policies directly"
   - Check **AmazonS3ReadOnly** policy
   - Click "Next: Review"
   - Click "Create user"
   - **Copy the credentials**
     - **Access key ID**
     - **Secret access key**
 - **Create ~/.aws/credentials file or paste the credentials in when you run the script**
   - Put the credentials you copied in the previous step here in this format:
```
[default]
aws_access_key_id = <your access key ID goes here>
aws_secret_access_key = <your secret_access_key goes here>
```
## Usage
`python s3inspector.py`

## Report example
![Sample report screenshot](https://github.com/kromtech/s3-inspector/blob/screenshot/samplerun.png "Sample report screenshot")


## s3inspector_lambda

Lambda function to perform the same check as above.

## Why?

Because as anyone who has taken an AWS exam knows, if the answer isn't Aurora, then its Lambda!

This also gives us a very simple route on incorporating other AWS services in place such as SNS and Scheduled Events 

## Lambda Setup & Prerequisites

Rather than a IAM user, we need a role that permits lambda execution as well as read-only access to S3 buckets. We also have to manually install the requests module

  - Create a directory called S3Inspector
  - cd into the directory
  - Copy the lambda function into the directory and then run the following command to install the module(s)
    
```bash
pip install requests -t .
```

  - Create a zipfile containing all the files and directories with everything in the root of the zipfile.

We can now create the lambda function

  - Go to the lambda console (https://console.aws.amazon.com/lambda/home)
  - Click on 'Create Function'
  - Click on 'Author from Scratch'
  - Give the function the name 's3inspector_lambda' (or the name of the file containing the function)
  - Apply the role created above
  - Click 'Create Function'
  - On the configuration page
    - Change the Code Entry Type to 'Upload from Zip'
    - Change the Runtime to 'Python 2.7'
    - Change the Handler to 's3inspector_lambda.lambda_handler'
  - Click 'Upload' and upload your zipfile
  - Increase the timeout of the function to something suitable for the number of S3 buckets in the account (I'm using 1 minute and 128Mb)

You can now run the function with an empty test event, or configure a trigger for the function.

## Todo & Limitations

The report is currently outputted into the console/logs of the function, the next release will include an SNS event being published containing the report.