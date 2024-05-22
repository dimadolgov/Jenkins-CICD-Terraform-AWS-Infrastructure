# Jenkins-CI\CD-Terraform-AWS-Infrastructure Project

This Terraform project automates the deployment of an AWS infrastructure designed to host a web server.

It sets up various components including a Virtual Private Cloud (VPC), Internet Gateway, Subnets, EC2 instance, Security Group, S3 bucket, and uses GitHub Webhook to trigger Jenkins CI\CD on commit.

## Table of Contents

- [File Structure](#file-structure)
- [Terraform Deployment](#terraform-deployment)
- [Getting Started](#getting-started)
- [Access the Web Server](#access-the-web-server)
- [Download SSH Key](#download-ssh-key)
- [Copy Private Key](#copy-private-key)
- [Go to Jenkins Website](#go-to-jenkins-website)
- [Final CI\CD using Jenkins](#final-cicd-using-jenkins)
- [Destroy Environment](#destroy-environment)
  
## File Structure

- `00-Provider`
  - Defines AWS provider configuration with the specified region.

- `01-Network`
  - Creates the VPC, Internet Gateway, Subnets, Route Tables, and associates them.

- `03-EC2`
  - Creates an EC2 instance with user data script for Apache installation and initial HTML file setup with Public IP (Not Elastic)

- `04-Security_Group`
  - Defines a Security Group with dynamic ingress rules for specified ports and CIDR blocks.

- `05-S3`
  - Creates an S3 bucket with a globally unique name.

- `06-SSH_Key`
  - Generates an RSA private key and associates it with an AWS key pair.

- `07-Outputs`
  - Defines an output for the public IP address of the EC2 instance.

- `08-variables`
  - Contains variable declarations for project configuration, network, EC2 instance, and Security Group.

- `template.tpl`
  - User data template used by the EC2 instance for initial configuration.


# Terraform Deployment

This repository contains Terraform scripts for deploying infrastructure on AWS.

## Getting Started

1. **Clone the project:**

    ```bash
    git clone https://github.com/dimadolgov/Jenkins-CICD-Terraform-AWS-Infrastructure.git
    cd AWS_IaC
    ```

2. **Add your administrative credentials:**

    Update either the "00-Provider" file or use the `aws configure` command to add your AWS credentials.

3. **Run Terraform:**

    Execute the following command to apply the Terraform configuration:
     ```bash
    terraform init
    ```
    ```bash
    terraform apply
    ```

    Follow the prompts to confirm the deployment.

## Access the Web Server

    After the deployment is complete, note the output variable "Web_Server_Public_IP." This is the external IP address of our web server, wait 5 minutes to let it boot. Browse to Jenkins WEB Using http://Web_Server_Public_IP:8080 ( Authentication is Disabled for todays use )

## Download SSH Key

    Access the S3 bucket named "nice-exam-s3-bucket" and download the SSH key with the name equals to our ec2 instance_id

## Copy Private Key

    Open the downloaded file and copy its contents. Starts with -----BEGIN RSA PRIVATE KEY----- ends with -----END RSA PRIVATE KEY-----

## Go to Jenkins Website
    ```bash
    http://IP:8080
    ```
## One Time Configuration is needed in Jenkins
   
   Go to Manage Jenkins>System Configuration>System
   
   Scroll down and Under Publish over SSH paste Private Key in Key field

   Under SSH Servers > Hostname enter the Web_Server_Public_IP you received after Terraform Deployment

   Name : nice-aws-web-server

   Hostname : Web_Server_Public_IP

   Username : ec2-user 
  
   Remote Directory : /var/www/html/

   Test Configuration > Should get **Success**

   Save and close Jenkins, Nothing to do there anymore | Browse the job `Webserver_index.html` if you wish
   
## Final CI\CD using Jenkins

    Do some changes to Index.html located in root folder, Push and Commit :)

    Jenkins will search for **"Hello World"** in Index.html ( If not found job will fail )

    ## Testing Code in Jenkins


    ```bash
    echo "--------------- Testing Started----------------"
    
    index_file="./index.html"
    if [ -e "$index_file" ]; then
        # Check if the word "Hello World" exists in the file
        if grep -q "Hello World" "$index_file"; then
            echo "Test passed: 'Hello World' found in $index_file"
            exit 0  # Exit with success status
        else
            echo "Test failed: 'Hello World' not found in $index_file"
            exit 1  # Exit with failure status
        fi
    else
        echo "Error: $index_file not found"
        exit 1  # Exit with failure status
    fi
    
    echo "--------------- Testing Ended ----------------"
   
## Destroy Environment
    ```bash
    cd AWS_IaC
    ```
    ```bash
    terraform destroy
    ```
