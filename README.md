# Hosting HTML Websites on Amazon EC2: S3 and GitHub Deployment Guide

This document provides instructions on how to host static HTML websites on an Amazon EC2 instance, either by downloading files from an Amazon S3 bucket or a GitHub repository.

## Prerequisites

* An active AWS account.
* An EC2 instance running Amazon Linux (or another suitable Linux distribution).
* Basic knowledge of Linux command-line interface.
* (For S3 Deployment) An S3 bucket containing your website files.
* (For GitHub Deployment) A GitHub repository with your website files.

## Deployment Options

### 1. Hosting from Amazon S3

This method downloads your website files from an S3 bucket to your EC2 instance and serves them using Apache HTTP Server.

#### Steps:

1.  **Create an IAM Role:**
    * Create an IAM role with `AmazonS3ReadOnlyAccess` permissions (or a custom policy with appropriate S3 access).
    * Attach this IAM role to your EC2 instance.

2.  **Connect to your EC2 Instance:**
    * Use SSH to connect to your EC2 instance.

3.  **Run the Deployment Script:**
    * Create a file named `deploy_s3.sh` and paste the following script into it:

    ```bash
    #!/bin/bash
    sudo su
    sudo yum update -y
    sudo yum install -y aws-cli httpd
    cd /var/www/html
    aws s3 sync "s3://bucketname/" /var/www/html
    unzip mole.zip
    cp -r /var/www/html/mole/* /var/www/html
    rm -rf mole.zip mole
    sudo systemctl enable httpd
    sudo systemctl start http
     
    * Replace `"your-s3-bucket-name"` with your actual S3 bucket name.
    * If your files are in a specific folder within the bucket, set the `S3_PREFIX` variable.

    * Make the script executable: `chmod +x deploy_s3.sh`
    * Run the script: `sudo ./deploy_s3.sh`

4.  **Configure Security Group:**
    * In the AWS Management Console, configure the security group of your EC2 instance to allow inbound HTTP traffic (port 80).

5.  **Access Your Website:**
    * Open a web browser and navigate to the public IP address of your EC2 instance.

### 2. Hosting from GitHub

This method downloads your website files from a GitHub repository to your EC2 instance and serves them using Apache HTTP Server.

#### Steps:

1.  **Connect to your EC2 Instance:**
    * Use SSH to connect to your EC2 instance.

2.  **Run the Deployment Script:**
    * Create a file named `deploy_github.sh` and paste the following script into it:

    ```bash
    #!/bin/bash
    sudo su
    yum update -y
    yum install -y httpd
    cd /var/www/html
    wget "<Github download zip URL>"
    unzip main.zip
    cp -r <your-repo-name>/* /var/www/html
    rm -rf "new unzipped folder" mole.zip
    systemctl enable httpd
    systemctl start httpd

    # --- Configuration ---
    GITHUB_REPO="<your-github-username>/<your-repo-name>" # Replace with your GitHub repository
    BRANCH="main"                                      # Replace with the branch you want to download
    LOCAL_WEB_ROOT="/var/www/html"                      # Directory on the EC2 instance to store website files
    WEB_SERVER_PORT="80"                              # Port for the HTTP server

   

    echo "Website files downloaded from GitHub and the Apache HTTP server has been started."
    echo "You should be able to access your website via the public IP address of your EC2 instance on port $WEB_SERVER_PORT (usually 80)."
    echo "Remember to configure the security group on your EC2 instance to allow inbound HTTP traffic on port $WEB_SERVER_PORT."
    ```

    * Replace `<your-github-username>/<your-repo-name>` with your actual GitHub username and repository name.
    * Replace `main` with the name of the branch you want to download, if it's not main.

    * Make the script executable: `chmod +x deploy_github.sh`
    * Run the script: `sudo ./deploy_github.sh`

3.  **Configure Security Group:**
    * In the AWS Management Console, configure the security group of your EC2 instance to allow inbound HTTP traffic (port 80).

4.  **Access Your Website:**
    * Open a web browser and navigate to the public IP address of your EC2 instance.
