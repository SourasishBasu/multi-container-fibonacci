# Fibonacci Sequence Calculator

> Web application for calculating fibonacci sequence value for a particular number with an overcomplicated tech stack for multi container production deployment testing

## Stack Used

- AWS RDS PostgreSQL database
- AWS ElastiCache Redis OSS Cache
- AWS Elastic Beanstalk w/ Docker on Amazon Linux 2
- AWS S3
- Github Actions CI/CD pipeline
- Travis CI deployment pipeline (Optional)
- Docker
- React, HTML, CSS

## AWS Configuration

### IAM Keys for Deployment

1. Search for the "IAM Security, Identity & Compliance Service"

2. Click "Create Individual IAM Users" and click "Manage Users"

3. Click "Add User"

4. Enter any name you’d like in the "User Name" field. (eg: docker-multi-travis-ci)

5. Click "Next"

6. Click "Attach Policies Directly"

7. Search for "beanstalk"

8. Tick the box next to "AdministratorAccess-AWSElasticBeanstalk"

9. Click "Next"

10. Click "Create user"

11. Select the IAM user that was just created from the list of users

12. Click "Security Credentials"

13. Scroll down to find "Access Keys"

14. Click "Create access key"

15. Select "Command Line Interface (CLI)"

16. Scroll down and tick the "I understand..." check box and click "Next"

> Copy and/or download the Access Key ID and Secret Access Key to add into the Github Actions Secrets along with the Dockerhub Username and Password to be used by the CI/CD pipeline for building images and pushing to AWS Elastic Beanstalk.

### Creating a Custom Security Group

1. Go to AWS Management Console and use Find Services to search for VPC

2. Find the Security section in the left sidebar and click Security Groups > Create Security Group button.

3. Set Security group name to multi-docker and Description to multi-docker.

4. Make sure VPC is set to your default VPC.

5. Scroll down and click the Create Security Group button.

6. After the security group has been created, find the Edit inbound rules button.

7. Click Add Rule. Set Port Range to 5432-6379.

8. Click in the box next to Source and start typing 'sg' into the box. Select the Security Group you just created.

9. Click the Save rules button

### Elastic Beanstalk Application Creation

1. Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

2. Click “Create Application”

3. Set Application Name to 'multi-docker'

4. Scroll down to Platform and select Docker on 64 bit Amazon Linux 2

5. Verify that "Single Instance (free tier eligible)" has been selected

6. Click the "Next" button.

7. In the "Service Role" section, verify that "Use an Existing service role" is selected.

8. Verify that aws-elasticbeanstalk-service-role has been auto-selected for the service role.

9. Verify that aws-elasticbeanstalk-ec2-role has been auto-selected for the instance profile.

10. Click "Skip to review" button.

11. Click the "Submit" button.

12. You may need to refresh, but eventually, you should see a green checkmark underneath Health.

### Applying Security Groups to Elastic Beanstalk

1. Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

2. Click Environments in the left sidebar.

3. Click MultiDocker-env. Click Configuration.

4. In the Instances row, click the Edit button.

5. Scroll down to EC2 Security Groups and tick the box next to multi-docker.

6. Click Apply and Click Confirm.

7. After all the instances restart and go from No Data to Severe, you should see a green checkmark under Health.

### Setting Environment Variables

1. Go to AWS Management Console and use Find Services to search for Elastic Beanstalk

2. Click Environments in the left sidebar.

3. Click MultiDocker-env.

4. In the left sidebar click Configuration.

5. Scroll down to the Updates, monitoring, and logging section and click Edit.

6. Scroll down to the Environment Properties section. Click Add environment property.

7. In another tab Open up ElastiCache, click Redis and check the box next to your cluster. Find the Primary Endpoint and copy that value but omit the :6379

    ```
    Set REDIS_HOST key to the primary endpoint listed above, remember to omit :6379

    Set REDIS_PORT to 6379

    Set PGUSER to postgres

    Set PGPASSWORD to postgrespassword

    Set the PGHOST key to the endpoint value listed above.

    Set PGDATABASE to fibvalues

    Set PGPORT to 5432
    ```

8. Click Apply button

9. After all instances restart and go from No Data, to Severe, you should see a green checkmark under Health.

### RDS Database Creation

1. Go to AWS Management Console and use Find Services to search for RDS

2. Click Create database button

3. Select PostgreSQL

3. In Templates, check the Free tier box.

4. Scroll down to Settings. Set DB Instance identifier to multi-docker-postgres

5. Set Master Username to postgres. Set Master Password to postgrespassword and confirm.

6. Scroll down to Connectivity. Make sure VPC is set to Default VPC

7. Scroll down to Additional Configuration and click to unhide. Set Initial database name to fibvalues

7. Scroll down and click Create Database button

### Applying Security Groups to RDS

1. Go to AWS Management Console and use Find Services to search for RDS

2. Click Databases in Sidebar and check the box next to your instance

3. Click Modify button.

4. Scroll down to Connectivity and add select the new multi-docker security group.

5. Scroll down and click the Continue button

6. Click Modify DB instance button and check Immediately Update.

### ElastiCache Redis Creation

1. Go to AWS Management Console and use Find Services to search for ElastiCache

2. In the sidebar under Resources, click Redis OSS caches

3. Click the Create Redis OSS caches button.

4. Select Design your own cache and Cluster cache. Make sure Cluster Mode is DISABLED.

5. Scroll down to Cluster info and set Name to multi-docker-redis.

6. Scroll down to Cluster settings and change Node type to `cache.t3.micro`

7. Change Number of Replicas to 0 (Ignore the warning about Multi-AZ).

8. Scroll down to Subnet group. Select Create a new subnet group if not already selected.

9. Enter a name for the Subnet Group such as redis.

10. Scroll down and click the Next button. Scroll down and click the Next button again. Scroll down and click the Create button.

### Applying Security Groups to ElastiCache

1. Go to AWS Management Console and use Find Services to search for ElastiCache

2. Under Resources, click Redis clusters in Sidebar

3. Check the box next to your Redis cluster.

4. Click Actions and click Modify

5. Scroll down to find Selected security groups and click Manage.

6. Tick the box next to the new multi-docker group and click Choose. 

7. Scroll down and click Preview Changes

8. Click the Modify button.

## Usage

- Go to the AWS Elastic Beanstalk application.

- It should say "Elastic Beanstalk is updating your environment"

- It should eventually show `Health - OK`.

- The application can now be accessed at the external URL provided under the environment name.

![Web App](https://github.com/user-attachments/assets/0fce3aa2-596f-4073-94df-7736bf2e4d13)

