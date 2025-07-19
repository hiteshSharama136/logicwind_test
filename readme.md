# AWS ECS-RDS Infrastructure with WAF, ALB, ECR, S3, RDS, and Secrets Manager

This CloudFormation template provisions a complete AWS infrastructure architecture suitable for a containerized application using ECS Fargate, RDS for PostgreSQL, ALB, WAF, ECR, S3, and secret rotation via Lambda.

---

## Main Components

- **Networking**: VPC, public/private subnets, route tables, IGW
- **Storage**: S3 bucket
- **Container Management**: ECS cluster, Fargate task definition and service
- **Database**: Amazon RDS for PostgreSQL
- **Security**: IAM roles, security groups, AWS WAF
- **Secrets Management**: AWS Secrets Manager with Lambda secret rotation
- **Load Balancing**: Application Load Balancer (ALB)

---

## Deployment Steps

### Step 1: Configure AWS CLI

```bash
aws configure
```

### Step 2: Prepare Lambda Code S3 Bucket

```bash
aws s3 mb s3://logicwind-script-bucket
aws s3 cp ezyzip.zip s3://logicwind-script-bucket/ezyzip.zip
```

### Step 3: Validate CloudFormation Template

```bash
aws cloudformation validate-template --template-body file://main.yaml
```

### Step 4: Prepare CloudFormation Template S3 Bucket

- Create a bucket and upload `main.yaml` file:

```bash
aws s3 mb s3://cf-logicwind-deployments
aws s3 cp main.yaml s3://cf-logicwind-deployments/main.yaml
```

### Step 5: Deploy the CloudFormation Stack

```bash
aws cloudformation create-stack --stack-name logicwind-infra --template-url https://cf-logicwind-deployments.s3.amazonaws.com/main.yaml --capabilities CAPABILITY_IAM --parameters ParameterKey=VpcCidr,ParameterValue=10.0.0.0/16 ParameterKey=PublicSubnet1Cidr,ParameterValue=10.0.1.0/24 ParameterKey=PublicSubnet2Cidr,ParameterValue=10.0.4.0/24 ParameterKey=PrivateSubnet1Cidr,ParameterValue=10.0.2.0/24 ParameterKey=PrivateSubnet2Cidr,ParameterValue=10.0.3.0/24 ParameterKey=DBUsername,ParameterValue=dbadmin ParameterKey=DBPassword,ParameterValue='YourSecurePassword123' ParameterKey=DBInstanceClass,ParameterValue=db.t3.micro
```

---

## Clean-up

To delete all resources provisioned by the stack:

```bash
aws cloudformation delete-stack --stack-name logicwind-infra
```

---

## Troubleshooting : Error that I faced at creation time

- **Lambda Deployment:** Ensure upload a `.zip` file for Lambda code (`.zip`), not a `.py` file.
- **Subnet/ALB/RDS Issues:** Both ALB and RDS require at least two subnets in different Availability Zones.
- **CloudFormation Errors:** Check the Events tab in AWS CloudFormation Console for detailed error messages.

---

