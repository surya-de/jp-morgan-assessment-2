# Assignment-2

This repository contains codes and responses to JP Morgans's internship assignment 2.

## Hands on with AWS

The questions asks us to answer the following question-
### 1. CloudFormation to configure AWS services
#### Approach
To decide a set of AWS services, I used the following 3 step process-
1. Firstly, I looked into the dashboard provided. This gave me an idea of the kind of aggregations that are made from the dataset.
2. Then, I analyzed the dataset and figured out the transformations that are required to make the aggregations easier. 
3. Finally, listed out a set of AWS services that can perform the required tasks and from that list I shortlisted the services that are **cost effective** and **can be implemented within the given time frame.**
#### A. Services used-
1. AWS S3
2. AWS Athena
3. AWS Sagemaker
4. AWS Lambda
5. AWS SQS

#### B. Reason behind the chosen services-
##### Design
![Architecture Diagram](/Screen%20Shot%202020-03-12%20at%203.02.53%20AM.png)
##### AWS S3
i. I have used S3 buckets to store the raw data(Landing bucket) and transformed data (Curated bucket).

ii. The solution I am offering is an automated solution with event triggers. So, I have one two extra S3 buckets. One to store the **lambda codes** and another one to sore the **Athena query results.**

##### AWS Athena
i. I am using Athena to perform ad hoc queries on the the transformed data to check if the data is fetching desired output.

ii. Athena is an AWS managed serverless query service hence I did not need to manage the underlying infrastructure.

iii. AWS Athena charges $5 on per TB of data scanned which can further be reduced by **partitioning the data** and **converting into columnar format(parquet etc).**
##### AWS SageMaker
i. I have used a notebook instance to write my python code to transform the input raw data and merge the two datasets into one unified data model.

ii. This notebook instance has been used in the development phase and I won't be using it during the deployment phase.

iii. The code developed using sage maker will be deployed on AWS lambda.
##### AWS Lambda
i. To create the automated data pipeline I wanted to leverage the serverless event driven Lambda platform.

ii. I have 2 lambda functions in my architecture. One is responsible for performing transformations on the raw data and the other is responsible to call Athena and query the database based on an event.
##### AWS SQS
i. For this assignment I tried to develop a pipeline which can perform the entire data ingestion and transformation task with a single click.

ii.In order to achieve that I needed to use SQS; a scalable queue service offered by AWS. 

iii. Using SQS I have created a trigger which calls a lambda function to query the Athene database when the data transformation task is completed.

### 2. Upload raw data into AWS service
#### Introduction
I have used **Python** to push the raw excel file into S3 bucket.
#### Modules used
1. AWS SDK for Python 3.6
2. Boto3 package

#### Prerequisite
To successfully run the python program, the user must have the following things configured.
1. **AWS CLI**
```bash
Mr-MacBook-Pro:codes Ra$ aws --version
aws-cli/1.16.289 Python/3.7.6 Darwin/18.7.0 botocore/1.13.37
```
2. AWS configure with **AWS Access key** and **AWS secret access id**.
```bash
Mr-MacBook-Pro:codes Ra$ aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                <not set>             None    None
access_key     ****************PIE6 shared-credentials-file    
secret_key     ****************DEVo shared-credentials-file    
    region                us-west-2      config-file    ~/.aws/config
```
3. **BOTO3** must be installed
```bash
pip install boto3
```
4. Python 3.6 or higher is required
```bash
Mr-MacBook-Pro:codes Ra$ python3 --version
Python 3.7.6
```
#### Code Snippet
To find the entire code look into the file **push_to_s3.py**
```python
'''
@Module: Module to push raw data to
			s3 bucket.
@Language: Python 3.7
@Author: Suryadeep(schatt37@asu.edu)
@Version: 1.0
@Author: Suryadeep(schatt37@asu.edu)
@Version: 1.2
'''

# Import Boto3 client for S3.
s3 = boto3.client('s3')
# This module uploads the raw data
# to s3 bucket.
def file_upload():
	try:
		s3.upload_file('../Data/county-to-county-2013-2017-current-residence-sort.xlsx', 
			'surya-landing','inflow/data.xlsx')
		print('1st file uploaded')
		
		s3.upload_file('../Data/county-to-county-2013-2017-previous-residence-sort.xlsx', 
			'surya-landing','outflow/data.xlsx')
		print('2nd file uploaded')
		
		return True
	
	except FileNotFoundError:
		print("Please check the location of your file")
		return False
	
	except NoCredentialsError:
		print("AWS credentials are not available")
		return False
```
