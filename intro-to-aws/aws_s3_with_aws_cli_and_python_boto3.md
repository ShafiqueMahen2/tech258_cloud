# Tech 258 - AWS S3 with AWS CLI & Python Boto3 SDK

- [Tech 258 - AWS S3 with AWS CLI \& Python Boto3 SDK](#tech-258---aws-s3-with-aws-cli--python-boto3-sdk)
  - [Preparing an EC2 instance to use](#preparing-an-ec2-instance-to-use)
    - [Steps](#steps)
  - [Using the AWS CLI for S3](#using-the-aws-cli-for-s3)
    - [Creating a S3 bucket](#creating-a-s3-bucket)
    - [Checking for a specific S3 bucket](#checking-for-a-specific-s3-bucket)
    - [Creating a sample file to put into our S3 bucket](#creating-a-sample-file-to-put-into-our-s3-bucket)
    - [Uploading the sample file to our S3 bucket](#uploading-the-sample-file-to-our-s3-bucket)
    - [Downloading files from our S3 bucket to our current directory](#downloading-files-from-our-s3-bucket-to-our-current-directory)
    - [Deleting a file from our S3 bucket](#deleting-a-file-from-our-s3-bucket)
    - [Deleting all files in our S3 bucket](#deleting-all-files-in-our-s3-bucket)
    - [Removing the S3 bucket itself](#removing-the-s3-bucket-itself)
  - [Using Python Boto3 SDK for S3](#using-python-boto3-sdk-for-s3)
    - [Check if Boto3 is installed on our instance](#check-if-boto3-is-installed-on-our-instance)
    - [Script: List all the S3 buckets](#script-list-all-the-s3-buckets)
    - [Script: Create an S3 bucket](#script-create-an-s3-bucket)
    - [Script: Upload data/file to the S3 bucket](#script-upload-datafile-to-the-s3-bucket)
    - [Script: Download/retrieve content/file from the S3 bucket](#script-downloadretrieve-contentfile-from-the-s3-bucket)
    - [Script: Delete content/file from the S3 bucket](#script-delete-contentfile-from-the-s3-bucket)
    - [Script: Delete all content/files from the S3 bucket](#script-delete-all-contentfiles-from-the-s3-bucket)
    - [Script: Delete the S3 bucket itself](#script-delete-the-s3-bucket-itself)


## Preparing an EC2 instance to use
Let's create an EC2 instance with AWS CLI and Python Boto3 configured.
### Steps
1) Update & upgrade local package base using the commands:
```
sudo apt update -y
sudo apt upgrade -y
```
2) Check if pythons installed using the command:
```
python3 –version
```
If we needed python3 we would run the following command:
```
sudo apt-get install python3
```
3) Install python package manager using the command:
```
sudo apt-get install python3-pip -y
```
4) Use PIP to install AWS CLI: 
```
sudo pip install awscli
```
5) To make the use of python commands simpler set an alias like below: 
```
alias python=python3
```
6) Login to AWS via CLI to gain access/permissions to do anything using the command: 
```
aws configure
```
What we need to configure?:
- `Access Key ID`: Your specific IAM user access key ID **KEEP THIS SAFELY HIDDEN**
- `Secret Access Key`: Your specific IAM user secret access key **KEEP THIS SAFELY HIDDEN**
- `Default region`: eu-west-1 (As we are deploying our services in Ireland region)
- `Default output format`: json
7) How to check if configuration worked? Run the command: 
```
aws s3 ls
```

**NOTE**: Once configured once, you won't have to configure everytime you start your instance.

## Using the AWS CLI for S3

To get familiar with all of the S3 commands we can run the following command: 
```
aws s3 help
```

### Creating a S3 bucket
To do this we would have to use the `aws s3 mb` command. Example usage below:
```
aws s3 mb s3://<name of bucket> --region <region e.g. us-west-1>
```
Example output: <br>
![aws_cli_mb_command.png](images/aws_cli_mb_command.png)

### Checking for a specific S3 bucket 
```
aws s3 ls s3://<name of bucket>
```
Example output: <br>
![aws_cli_ls_command.png](images/aws_cli_ls_command.png)
### Creating a sample file to put into our S3 bucket 
```
echo This is the first line in a test file > test.txt
```

### Uploading the sample file to our S3 bucket
```
aws s3 cp <filename> s3://<bucket name>
``` 
Example output: <br>
![aws_cli_cp_command.png](images/aws_cli_cp_command.png)

### Downloading files from our S3 bucket to our current directory
```
aws s3 sync s3://<bucket name> .
```
Example output: <br>
![aws_cli_sync_command.png](images/aws_cli_sync_command.png)

Can check using: `ls` Example output: <br>

### Deleting a file from our S3 bucket 
```
aws s3 rm s3://<name of bucket>/<name of file to remove>
```
Example output: <br>
![aws_cli_rm_command.png](images/aws_cli_rm_command.png)

:exclamation_mark: Warning! Will delete the file specified from the bucket without asking you to confirm. :exclamation_mark:

### Deleting all files in our S3 bucket
```
aws s3 rm s3://<bucket name> --recursive
```
Example output: <br>
![aws_cli_rm_recursive_command.png](images/aws_cli_rm_recursive_command.png)

:warning: Danger! Will delete everything in the bucket without asking you to confirm. :warning:

### Removing the S3 bucket itself
```
aws s3 rb s3://<bucket name>
```

If trying to delete when bucket isn't empty it won't let you delete the bucket. To force delete use the `--force` flag.

You can check if the bucket is deleted by using the `aws s3 ls` command.

Example output: <br>
![aws_cli_rb_command.png](images/aws_cli_rb_command.png)

:boom: Extreme danger! Will delete the bucket **AND** everything in the bucket without asking you to confirm. :boom:

## Using Python Boto3 SDK for S3
We can also manage our AWS resources programatically. To do this we can leverage the Python SDK for AWS: Boto3.

### Check if Boto3 is installed on our instance
To do this run the following command:
```
pip show boto3
```

If Boto3 is not installed run the following command to install it using pip:
```
sudo pip install boto3
```

### Script: List all the S3 buckets
We can create a script to do this for us. Let's name it `list_buckets.py`.
Script:
```python
import boto3

# Create an S3 client
s3 = boto3.client('s3')

# List all S3 buckets
response = s3.list_buckets()

# Print bucket names
print("List of S3 Buckets:")
for bucket in response['Buckets']:
    print(bucket['Name'])
```

### Script: Create an S3 bucket
We can create a script to do this for us. Let's name it `create_bucket.py`.
Script:
```python
import boto3

# Create an S3 client
s3 = boto3.client('s3')

# Specify a bucket name
bucket_name = 'tech258-shafique-test-boto3'  

# Create S3 bucket
s3.create_bucket(Bucket=bucket_name, CreateBucketConfiguration={'LocationConstraint': region})
print(f"Bucket '{bucket_name}' created successfully in region {region}.")
```

### Script: Upload data/file to the S3 bucket
We can create a script to do this for us. Let's name it `upload_file_to_s3.py`.
Script:
```python
import boto3

# Create an S3 client
s3 = boto3.client('s3')

# Specify the bucket name and file to upload
bucket_name = 'tech258-shafique-test-boto3'  
file_path = '/path/to/your/file.txt'  # Path to your local file

# Upload file to S3
s3.upload_file(file_path, bucket_name, 'file.txt')  # 'file.txt' is the S3 key
print("File uploaded successfully.")
```

### Script: Download/retrieve content/file from the S3 bucket
We can create a script to do this for us. Let's name it `download_file_from_s3.py`.
Script:
```python
import boto3

# Create an S3 client
s3 = boto3.client('s3')

# Specify the bucket name and file to download
bucket_name = 'tech258-shafique-test-boto3'  
file_key = 'file.txt'  # Key of the file to download in S3
local_file_path = '/path/to/save/downloaded_file.txt'  # Local path to save downloaded file

# Download file from S3
s3.download_file(bucket_name, file_key, local_file_path)
print("File downloaded successfully.")
```

### Script: Delete content/file from the S3 bucket
We can create a script to do this for us. Let's name it `delete_file_from_s3.py`.
Script:
```python
import boto3

# Create an S3 client
s3 = boto3.client('s3')

# Specify the bucket name and file to delete
bucket_name = 'tech258-shafique-test-boto3'  
file_key = 'file.txt'  # Key of the file to delete in S3

# Delete file from S3
s3.delete_object(Bucket=bucket_name, Key=file_key)
print("File deleted successfully.")
```

### Script: Delete all content/files from the S3 bucket
We can create a script to do this for us. Let's name it `delete_all_from_s3_bucket.py`.
Script:
```python
import boto3    

s3 = boto3.client('s3')

bucket_name = 'tech258-shafique-test-boto3'

bucket_name.objects.all().delete()
```

### Script: Delete the S3 bucket itself
We can create a script to do this for us. Let's name it `delete_s3_bucket.py`.
Script:
```python
import boto3

# Create an S3 client
s3 = boto3.client('s3')

# Specify the bucket name to delete
bucket_name = 'tech258-shafique-test-boto3'

# Delete the S3 bucket
s3.delete_bucket(Bucket=bucket_name)
print(f"Bucket '{bucket_name}' deleted successfully.")
```