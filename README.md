# Setting Up an NBA Data Lake with AWS Services
![Cover Image](https://media2.dev.to/dynamic/image/width=1000,height=420,fit=cover,gravity=auto,format=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Ffz439zose42lckcm4rfu.png)

## Introduction
In this article, we'll walk through how to set up an NBA data lake using AWS services. A data lake is a centralized repository that allows you to store structured and unstructured data at any scale. By leveraging AWS services such as Amazon S3, AWS Glue, and Amazon Athena, we can automate the creation of a scalable and efficient data lake for NBA analytics. This setup enables seamless data storage, transformation, and querying to facilitate insightful analysis.

## Overview
The `setup_nba_data_lake.py` script automates the process of creating a data lake for NBA-related data. Specifically, it performs the following actions:[**Repo**](https://github.com/Vivixell/NBA_DATA_LAKE.git)

- Creates an Amazon S3 bucket to store raw and processed NBA data.
- Uploads sample NBA data (in JSON format) to the S3 bucket.
- Configures AWS Glue to create a database and an external table for structured querying.
- Integrates Amazon Athena for querying NBA data efficiently using SQL.

By the end of this setup, users will have a fully functional data lake that can be queried using Athena, making it easier to analyze NBA statistics and trends.

## Prerequisites
Before running the script, ensure you have the following:

#### AWS Account
You must have an active AWS account to create and manage the required resources.

#### SportsData API Key
- Create a free account at [SportsData](SportsData.io).
- Navigate to "Developers" > "API Resources" > "Introduction & Testing."
- Sign up for the "SportsDataIO API Free Trial," selecting NBA as the preferred data source.
- Retrieve your API key from "Query String Parameters."

#### IAM Role/Permissions
Ensure the IAM user or role executing the script has the following permissions:

- **S3:** `s3:CreateBucket`, `s3:PutObject`, `s3:DeleteBucket`, `s3:ListBucket`
- **Glue:** `glue:CreateDatabase`, `glue:CreateTable`, `glue:DeleteDatabase`,`glue:DeleteTable`
- **Athena:** `athena:StartQueryExecution`, `athena:GetQueryResults`

You can create a policy using the policy file from the [repository](https://github.com/Vivixell/NBA_DATA_LAKE.git) and apply it to the user.

If you're using a different S3 bucket name, update all instances of "victor-analytics-data-lake" accordingly:

- **In the IAM policy** - Modify the bucket name under the Resource field.
- **In the script for resource deletion** - Update the bucket name declaration.
- **In the `setup_nba_data_lake.py` script under the bucket name section** - Ensure it matches your chosen bucket name to avoid execution errors.

Below is a screenshot highlighting all areas that need to be updated if using a different S3 bucket name. If you are using "victor-analytics-data-lake", you can ignore this step.


![IAM Policy](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/smp1dfjrj75l4uiecysp.png)


![Script for deleting resource](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4fqd3558li34s2nxma67.png)


![Script for creating resource](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/95ema8ra3r4yqjuqy2w1.png)

#### AWS CloudShell or CLI
Use AWS CloudShell or set up the AWS CLI on your local machine to execute the script.

With these prerequisites in place, you're ready to start setting up the NBA data lake. In the next section, I'll guide you through the step-by-step implementation.

## Step-by-Step Guide

### Step 1: Log in to Your AWS Console
Go to [AWS](http://aws.amazon.com/) and sign in to your account. At the top, next to the search bar, you will see a square icon with a >_ inside. Click this to open AWS CloudShell.

![CloudShell Navigation](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ovksokx8zfxq8958exsm.png)

### Step 2: Create a .env File

In the CLI (Command Line Interface), type:

```
nano .env
```
Paste the following lines into your file, replacing `your_sportsdata_api_key` with your actual API key:

```
SPORTS_DATA_API_KEY=your_sportsdata_api_key
NBA_ENDPOINT=https://api.sportsdata.io/v3/nba/scores/json/Players

```


![Cloudshell terminal](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/579errprf4jo4gaq7q75.png)

Note: If you're not comfortable using Nano, prepare the content in a text editor before pasting it into the terminal.
Press `Ctrl + O` to save, then `Enter`, and `Ctrl + X` to exit Nano.

### Step 3: Create the setup_nba_data_lake.py File
- In the CLI, type: 

```
nano setup_nba_data_lake.py
```
- In another window, visit the [GitHub](https://github.com/Vivixell/NBA_DATA_LAKE.git) repository.
- Copy the contents of the `setup_nba_data_lake.py` file. (Don't forget to edit the bucket name in the code if you want to.)
- Return to the CloudShell window and paste the contents into your file.
- Save and exit the file.

![Edit Bucket Name](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a7q6k2xvfqd05ahrir7k.png)

### Step 4: Run the Script
Run the following command:

```
python3 setup_nba_data_lake.py
```
You should see messages confirming that the resources were successfully created, the sample data was uploaded, and the Data Lake setup was completed.

_**Troubleshooting:**_ If you encounter an error about `dotenv` not being installed, run: `pip3 install python-dotenv` and then rerun the previous command.

![Error in script](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yersw79cidkn3flrr82j.png)

![Correcting error and reruning script](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/g5fgx7kkwsezlat2ufnn.png)

With the script successfully executed, everything is set up and can be manually verified in the AWS Console.

### Step 5:Step 5: Manually Check for Resources
- In the AWS search bar, type `S3` and click on the S3 service.
- Locate the bucket named **victor-analytics-data-lake** and click on it.
- Inside the bucket, you should see two objects:

![S3 Bucket](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ozloumi8kjzexjnuaf0s.png)

![S3 Bucket Content](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/bxsvc7ysw000pal0vpoq.png)

- Click on `raw-data` and you will see it contains `nba_player_data.json`, and at the top, select **Open** to view the file contents. You should see various NBA player data in JSON format.


![View Jason file](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sm8o1yw7l6kgp7x0icmu.png)

- You'll see a long string of various NBA data

![Json file](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3yvagkh3vpbo5qnpadar.png)

### Querying Data in Amazon Athena
To analyze the data, follow these steps:
- Go to the **AWS Athena** console.

![Athena Console](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xewqx8uuvmafhi8kjvip.png)

- In the left panel, select the database you created (e.g., `glue_nba_data_lake`).

![Athena database](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c1calwnl8dagk8zo2v1y.png)

- Set up the Athena query result location:
  - Navigate to **Settings**.
  - Set **Query result location** to: `s3://victor-analytics-data-lake/athena-results/`
  - Click **Save**.

![Result Location](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vvlguv01cwd2eur4yx42.png)

- In the **Editor** tab, paste the following SQL query to retrieve point guards (PGs):

```
SELECT FirstName, LastName, Position, Team
FROM nba_players
WHERE Position = 'PG';
```
- Click **Run** Query. The results will be stored in S3 and displayed under **Query Results**.

![Result 1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/urh87aui2kf4qzl5z9ch.png)

- To verify the data, run: 

```
SELECT * FROM nba_players LIMIT 5;
```
- You should see a sample of player data.

![Result 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hze7s495h6tptxv21dpa.png)

### Deleting Resources
To delete and clean up all the resources created, follow these steps:

- Copy the contents of `delete_aws_resource.py` from the online repository.
- In the terminal, create a new file by running:

```
nano delete_aws_resource.py
```

- Paste the copied content into the file, save, and exit.
- Run the script to remove all resources:

```
python3 delete_aws_resource.py
```
- This will ensure a clean teardown of all the resources provisioned during the setup.


## What We Learned
- Securing AWS services using least-privilege IAM policies.
- Automating resource creation using a script.
- Integrating external APIs into cloud-based workflows.

## Future Enhancements
- Automate data ingestion using AWS Lambda.
- Implement a data transformation layer with AWS Glue ETL.
- Add advanced analytics and visualizations using AWS QuickSight.






















