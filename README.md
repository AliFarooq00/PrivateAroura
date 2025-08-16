# Aurora PostgreSQL Public Demo CloudFormation

This project provides an AWS CloudFormation template to set up a secure, private Aurora PostgreSQL cluster with public connectivity for database management tools (such as pgAdmin) via a bastion EC2 host. It demonstrates core AWS networking concepts, security group configuration, and Aurora cluster deployment.

## Core Concepts

- **VPC & Subnets**: Creates a dedicated VPC with public and private subnets across three Availability Zones for high availability.
- **Internet Gateway & Route Tables**: Public subnets are routed to the internet via an Internet Gateway.
- **Security Groups**: 
  - Aurora DB security group restricts access to the database port (5432).
  - EC2 security group allows SSH (port 22) from anywhere.
  - Rules allow the EC2 bastion host to connect to Aurora.
- **Aurora Cluster**: Deploys an Aurora PostgreSQL cluster in private subnets for security.
- **EC2 Bastion Host**: Launches an EC2 instance in a public subnet to act as a jump host for database management.
- **Key Pair**: Creates an EC2 key pair for SSH access to the bastion host.

## Setup Instructions

### Prerequisites

- AWS account with permissions to create VPC, EC2, RDS, and related resources.
- AWS CLI or AWS Management Console.
- (Optional) [pgAdmin](https://www.pgadmin.org/) or another PostgreSQL client.

### Deployment Steps

1. **Clone the Repository**
   ```
   git clone 
   cd PrivateAroura
   ```

2. **Deploy the CloudFormation Template**
   - Using AWS Console: Upload `template.yaml` and launch a new stack.
   - Using AWS CLI:
     ```
     aws cloudformation create-stack --stack-name aurora-public-demo \
       --template-body file://template.yaml \
       --capabilities CAPABILITY_IAM
     ```

3. **Access the EC2 Bastion Host**
   - Download the generated key pair (`AuroraEC2JumpHostKeyPair`) from AWS EC2 > Key Pairs.
   - SSH into the EC2 instance:
     ```
     ssh -i AuroraEC2JumpHostKeyPair.pem ec2-user@<EC2_Public_IP>
     ```

4. **Connect to Aurora PostgreSQL**
   - Use pgAdmin or `psql` from the bastion host to connect to the Aurora cluster endpoint.
   - Retrieve the cluster endpoint and credentials from the CloudFormation Outputs or AWS Console.

### Security Notes

- The Aurora cluster is deployed in private subnets for security.
- Only the EC2 bastion host can access the database port (5432).
- SSH access to the bastion host is open to the internet; restrict this in production.
- Database credentials are managed securely via AWS Secrets Manager.

## Outputs

- **rdsClusterArn**: ARN of the Aurora cluster.
- **rdsClusterSecretArn**: ARN of the master user secret.

## Cleanup

To delete all resources:
```
aws cloudformation delete-stack --stack-name aurora-public-demo
```

## Customization

- Change subnet CIDR blocks or instance types as needed.
- Adjust security group rules for tighter access control.
- Use your own AMI for the EC2 bastion host if required.

## License

MIT

## Author

GitHub Copilot