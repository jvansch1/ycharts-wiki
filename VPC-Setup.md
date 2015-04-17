# Staging

## Create VPC

Name | CIDR | Tenancy
---- | ---- | -------
staging | 10.1.0.0/16 | Default

## Create Subnets

Name | VPC | Availability Zone | CIDR
---- | --- | ----------------- | ----
staging_public_subnet_d | staging | us-east-1d | 10.1.0.0/24
staging_public_subnet_e | staging | us-east-1e | 10.1.5.0/24
staging_persistent_subnet_d | staging | us-east-1d | 10.1.1.0/24
staging_autoscale_subnet_d | staging | us-east-1d | 10.1.2.0/24
staging_db_subnet_d | staging | us-east-1d | 10.1.3.0/24
staging_db_subnet_e | staging | us-east-1e | 10.1.4.0/24

## Create Internet Gateway

Name | VPC
---- | ---
staging_internet_gateway | staging

> NOTE: You may need to manually attach the internet gateway to the VPC *after* creation.

## Create Route Tables

> DO NOT touch the `Main` route table! It should only allow internal routes by default.

### Public Route Table

Create a route table named `staging_public_route_table` for the staging VPC with the following routes:

Destination | Target | Status | Propagated
----------- | ------ | ------ | ----------
10.1.0.0/16 | local  | Active | No
0.0.0.0/0   | staging_internet_gateway | Active | No

Associate this route table with the `staging_public_subnet_d` and `staging_public_subnet_e` subnets *only*.

### Private Route Table

Create a route table named `staging_private_route_table` for the staging VPC with the following routes:

Destination | Target | Status | Propagated
----------- | ------ | ------ | ----------
10.1.0.0/16 | local  | Active | No

Associate this route table with all other subnets.

## Create Security Groups

Create the security groups now. You will update the security groups' rules later.

Name | Group Name | Description | VPC
---- | ---------- | ----------- | ---
ssh  | ssh | SSH Access for NAT | staging
http | http | HTTP/S Access for ELBs | staging

## Create Load Balancers

Create a public facing load balancer in the staging VPC and attach the two public subnets.

## Create NAT Machine

Detailed instructions [here](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_NAT_Instance.html#NATSG).

1. Create a new instance.
1. Search for `amzn-ami-vpc-nat` and select the latest ami with HVM.
1. Select the staging VPC and the `staging_public_subnet_d` subnet.
1. Select the `ssh` and the `default` security groups.
1. Associate (and create if necessary) and elastic IP to the new instance.
1. Disable `SrcDestCheck` by selecting **Change Source/Dest. Check** from the **Actions** menu.
1. Update the `staging_private_route_table` to add the following route:

    Destination | Target | Status | Propagated
    ----------- | ------ | ------ | ----------
    0.0.0.0/0   | NAT instance | Active | No

## Create Temporary Database and Cache

### Create DB Subnet Groups

Create a DB Subnet Group in RDS named `staging_db_subnet_group` for the staging VPC with the following subnets:

Availability Zone | Subnet ID | CIDR
----------------- | --------- | ----
us-east-1d | staging_db_subnet_d | 10.1.3.0/24
us-east-1e | staging_db_subnet_e | 10.1.4.0/24

### Restore DB

Restore the database from a snapshot into the staging VPC with no multi-AZ deployment and the subnet group above.

### Create Cache Subnet Group

Create a Cache Subnet Group in Elasticache named `staging-cache-subnet-group` for the staging VPC with the following subnets:

Availability Zone | Subnet ID | CIDR
----------------- | --------- | ----
us-east-1d | staging_db_subnet_d | 10.1.3.0/24

### Restore Redis

Restore both redis machines from snapshots into the staging VPC with the subnet group above.

## Build All Machines

Use the chef solo machine scripts to build all the machines in the correct subnet.

## Big Switch

1. Stop both EC2-Classic staging and VPC staging.
1. Make snapshots of database and redis.
1. Restore these new snapshots using same method above.
1. Update references to new database and redis servers.
1. Start everything.
