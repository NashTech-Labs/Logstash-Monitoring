# Logstash Monitoring #

## STEPS FOR MONITORING ##

### STEP-1: Set Up AWS CLI ###

1. Log in to the Jenkins server via SSH.
2. Install the AWS CLI using the appropriate package manager for your OS using command below:

```
sudo apt-get install awscli
```

### STEP-2: Create EC2 Instance and Configure ELK ###

Follow all the necessary steps to create an EC2 Instance and configure ELK on that machine which should be running.

### STEP-3: Configure Jenkins SSH Credentials ###

Now, we need to configure Jenkins to use SSH private key credentials to connect to the EC2 instance.

1. Log in to the Jenkins server.
2. Navigate to the Jenkins dashboard and click on “Credentials” in the left sidebar.
3. Click on “Global credentials (unrestricted)”.
4. Click on “Add Credentials”.
5. Choose “SSH Username with private key” as the kind.
6. Provide a username (e.g., “demo”) and paste the private key contents into the “Private Key” field.
7. Click “OK” to save the credential.
8. Add the public key to the instance using command:

```
ssh-copy-id -i /path/to/private_key.pem ec2-user@EC2_INSTANCE_IP
```
Replace the /path/to/private_key.pem, ec2-user, and EC2_INSTANCE_IP with the appropriate values of yours.

### STEP-4: Create the Jenkins Pipeline

1. Log in to the Jenkins server “Dashboard”.
2. Click on “New Item” to create a new Jenkins pipeline job.
3. Enter a suitable name for the pipeline job (e.g., “Logstash Monitoring Pipeline”).
4. Select “Pipeline” as the job type and click “OK”.
5. Creating Jenkins Pipeline 
6. In the pipeline configuration page, scroll down to the “Pipeline” section.
7. Set the “Definition” to “Pipeline script from SCM”.
8. Choose the appropriate SCM system (e.g., Git) and provide the repository URL containing the Jenkinsfile (pipeline script).
9. Give the “Repository URL” & Add “Credentials” for git
10. Save the configuration by clicking on “Save”.

### STEP-5: Configure the Jenkinsfile ###

Update the below points in the pipeline

*instanceId:* Replace with the actual EC2 instance ID.

*logstash:* Update the path to the private key used for SSH connection.

### STEP-6: Save the configuration & trigger the Pipeline ###

Save the Jenkins pipeline configuration and trigger the pipeline manually or schedule it to run periodically.

## Checks State ## 

#### 1. Check EC2 Instance State ####

Retrieves the state of the specified EC2 instance.

#### 2. SSH Connectivity Check ####

Validates SSH connectivity to the EC2 instance. It uses the private key configured in Jenkins credentials.

#### 3. Logstash Status Check ####

Verifies whether Logstash is running on the EC2 instance.

#### 4. Alert on Failure ####

If any check fails, an alert is sent to the configured Office 365 Teams channel. 
