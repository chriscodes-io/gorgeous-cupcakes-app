# Gorgeous Cupcakes - Multi-Tier AWS Deployment

## Overview

This repository contains the source code for the "Gorgeous Cupcakes" PHP application. The infrastructure required to run this application is defined using AWS CloudFormation in a multi-tier architecture.

The deployment utilises a modular, three-stack approach for separation of concerns and reusability.

## Architecture

The application is deployed across a custom Virtual Private Cloud (VPC) with the following structure:
* **Web Tier:** A single EC2 instance running Amazon Linux 2023 with an Apache web server. It resides in a Public Subnet to be accessible from the internet.
* **Database Tier:** An AWS RDS for MySQL instance. It is placed in a Private Subnet for security, accessible only from the EC2 instance.

## Prerequisites

* An active AWS Account.
* An EC2 Key Pair created in your target AWS region.
* The three CloudFormation templates (`cf_vpc.yaml`, `cf_rds.yaml`, `cf_ec2 - db.yaml`).

## Deployment Steps

The infrastructure must be deployed by creating the CloudFormation stacks in the following order:

1.  **Deploy the Network Stack:**
    * Create a new stack using the `cf_vpc.yaml` template.
    * Give the stack a name (e.g., `GorgeousCupcakes-VPC`) and note it for the next steps.

2.  **Deploy the Database Stack:**
    * Create a new stack using the `cf_rds.yaml` template.
    * When prompted for parameters, enter the name of the network stack you just created.

3.  **Deploy the Web Server Stack:**
    * Create a final stack using the `cf_ec2 - db.yaml` template.
    * When prompted, enter the name of the network stack again.

## Post-Deployment Configuration

After all three stacks show a `CREATE_COMPLETE` status:

1.  **Find the EC2 Public IP:** Navigate to the EC2 console and copy the Public IPv4 address of your new instance.
2.  **Connect via SSH:** Use your key pair to SSH into the instance (`ssh -i "your-key.pem" ec2-user@<your-ip-address>`).
3.  **Import the Database:** Run the `mysql` command from the EC2 terminal to import the SQL schema and data into your RDS database.
4.  **Update Config File:** Edit the `/var/www/html/model/database.php` file on the server and replace the placeholder credentials with your live RDS endpoint, username, and password.

## Accessing the Application

Once configured, the application can be accessed by navigating to the Public IPv4 address of the EC2 instance in a web browser: `http://<your-ip-address>`.
