# Elastic Container Service

## Lab 1: Create ECS Cluster

### Steps: 
1. Go to AWS console and nav to `ECS Service`
2. The wizard at the ECS landing page encourages you to use `Fargate` and begin by building a Task Definition. We are going to avoid this for now, to create a ECS cluster manually to see how an ECS clusteer is made up. Click on `Cancel` on the bottom of the landing page. 
3. When redirected, your first step will be to create an `ECS Cluster`. Click `Create Cluster`.
4. When redireceted, you can see that there are 3 possible cluster templates you can select from. 
    + `Networking Only` (Using Fargate)
    + `EC2 Linux + Networking`
    + `EC2 Windows + Networking`

> NOTE: For this excercise we want to set up the cluster manually to see the mechanics of what `Fargate` will do for us. So `DO NOT` select item 1, which is recommended by AWS. Instead we will select item 2 to set up the cluster without Fargate.

5. Select `EC2 Linux + Networking`, and click `Next Step`. 
6. When redirected, you will need to complete the Cluster configuration.
    + Add a cluster name, for this example call it `demo-cluster`
    + For EC2 provisioning you can specify 1. `On-Demand` or 2. `Spot` instances. For the sake of this demo we will select `On-Demand`
    + For the EC2 Type, we want to select `t2.micro`. 
    + Leave the `Enable T2 unlimited` box __unchecked__. 
    + Number of Instances, you can simply leave as `1`
    + For the `EC2 AMI ID`, note that within the default name provided you see `...amazon-ecs-optimized...`. This demonstrates that for AWS ECS there is a special AMI for container hosting.
    + EBS storage will be relative to the EC2 instance type selected. For the `t2.micro` you will see storage of 22 GiB, which is fine. 
    + You will have to utilze a `Key Pair` for access to the EC2. If you have an existing one, you can specify in the drop-down or create a new one. 
7. On the same page, you will also have to configure the ECS cluster Networking. 
    + On the `VPC` field, simply select your `default VPC`
    + With the `default VPC`, depending on the AWS region, you should have multiple subnets you can select. Select the max number of subnets available to you. 
    + For the security group field, because this is a demo, you can either reuse a security group, or create a new one. If you create a new security group, you will be prompted for `Security Group Inbound Rules`. For 1. `CIDR Block` enter `0.0.0.0` & 2. `Port Range` enter 22
8. On the same page, you will need to provision an `IAM Role`. This is because the ECS Agent will make calls to the ECS API on your behalf, so it will need a permission allowing these resources to talk to each other. The Role you need to provision is listed in the description as `ecsInstanceRole`. By default ECS will provision for you, so you can simply leave the field as default. 
9. If you wish to add Tags you can do so, or leave as default. 
10. Click `Create`. 
11. When redirected, you will see a status page of several items: 
    + __ECS Cluster__ 
    + __ECS Instance IAM Policy__
    + __CloudFormation Stack__
12. When these items all turn to green, you will be able to view the Cluster created. 

-----

### What did you learn... 

1. You learned that `AWS ECS` is a managed service that can be used to create a cluster of EC2 hosts. Here we provisioned a cluster of 1 EC2 host, using a __container optimized__ AMI, configured the cluster to be deployed across 3 subnets within our VPC. We provisioned provisioned ephimerial storage, assigned an IAM policy and deployed an ECS Agent (aka Docker container). 

2. You learned that deployment of AWS resources always involves mulitple AWS services that are combined to execute a task. 

    1. We used `AWS EC2` for our virtual host
    2. We used `AWS VPC` to provision Subnets and our Default Network configuration
    3. We used `AWS IAM` to provision roles and permissions
    4. We used an `AWS AMI` to provision a container optimized EC2 configuration
    5. We used `AWS CloudFormation` to create a IaC template to deploy AWS resources
    6. We have metrics enabled via `AWS CloudWatch` 

