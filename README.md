## AWS CloudFormation template to launch a CouchDB instance on EC2, running on Docker.

This CloudFormation template is designed to create a VPC, two subnets in different availability zones, a security group, and an EC2 instance running Apache CouchDB in the first subnet.

## Parameters

- **EnvironmentPrefix**: A string to prefix the names of resources created by this template. Default is "test-".
- **ImageId**: The Amazon Machine Image (AMI) ID to use for the EC2 instance. Default is the Amazon Linux 2 AMI.
- **KeyName**: The name of the EC2 key pair to use for the instance.
- **InstanceType**: The EC2 instance type. Default is t3.medium.
- **LocalZone**: The local zone identifier. Default is "-lax-1".
- **InboundAdminCIDR**: The CIDR block for the admin network that should be allowed access to the EC2 instance.

## Resources

### VPC and Subnets

- **Ec2VPC**: A new VPC with CIDR block 10.0.0.0/16, DNS hostnames, and DNS support enabled.
- **Ec2SubnetOne**: A new subnet in the first local zone with CIDR block 10.0.64.0/18, associated with the VPC and configured to map public IPs on launch.
- **Ec2SubnetTwo**: A new subnet in the second local zone with CIDR block 10.0.128.0/18, associated with the VPC and configured to map public IPs on launch.

### Routing

- **StackRouteTable**: A route table associated with the VPC.
- **StackRouteExternal**: A default route pointing to the internet gateway.
- **SubnetRouteAssocA**: Association of the route table with the first subnet.
- **SubnetRouteAssocB**: Association of the route table with the second subnet.

### Internet Gateway

- **TheInternetGateway**: An internet gateway with a name based on the environment prefix.
- **IgwAttachment**: An attachment of the internet gateway to the VPC.

### Security Group

- **Ec2SecurityGroup**: A security group allowing ICMP (ping) and TCP traffic on port 51311 from the specified CIDR block.

### IAM

- **Ec2InstanceProfile**: An IAM instance profile for the EC2 instance.
- **IamInstanceRole**: An IAM role for the EC2 instance with policies allowing ECS, logs, ECR, and SSM actions.

### EC2 Instance

- **EC2Instance**: An EC2 instance using the specified AMI, key pair, instance type, and security group, with an IAM instance profile, and UserData for installing and configuring CouchDB.

## Usage

To create a stack using this template, you can use the AWS Management Console, AWS CLI, or SDKs.

For example, using the AWS CLI:

```bash
aws cloudformation create-stack --stack-name your-stack-name --template-body file://template.yaml --parameters ParameterKey=KeyName,ParameterValue=your-key-pair-name ParameterKey=InboundAdminCIDR,ParameterValue=your-cidr-block
```

Replace `your-stack-name`, `your-key-pair-name`, and `your-cidr-block` with appropriate values.