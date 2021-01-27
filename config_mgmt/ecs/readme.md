# Elastic Container Service (ECS)

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
13. Review what you created by selecting the `cluster-demo` link. At the bottom of the dashboard you will see several tabs. Select `ECS Instances`. 
    + Here you see a `Container Instance` id, demonstrating that there is a Docker Image deployed to the EC2 instance (aka ECS Agent). The id is a hyperlink that will take you to the details of this container image. If you scroll right on the table you wlll also see `Agent Version` demonstrating the version of the container, and `Docker version` demonstrating the verision of the Docker client running on the EC2 host.
    + You can also see a `EC2 Instance` id, demonstrating that we used an EC2 AMI to create a virtual host that houses the docker image. This is also a hyperlink that will redirect to the EC2 service with details of the virtual host. If you scroll right on the table you can also see `CPU Available` & `Memory Available` on the virtual host.

-----

### What did you learn:

![image](https://user-images.githubusercontent.com/8760590/106005913-f1d87d80-6071-11eb-9777-9f02d7e47f1d.png)

1. You learned that `AWS ECS` is a managed service that can be used to create a cluster of EC2 hosts. Here we provisioned a cluster of 1 EC2 host, using a __container optimized__ AMI, configured the cluster to be deployed across 3 subnets within our VPC. We provisioned provisioned ephimerial storage, assigned an IAM policy and deployed an ECS Agent (aka Docker container). 

2. You learned that deployment of AWS resources always involves mulitple AWS services that are combined to execute a task. 

    - [ ] We used `AWS EC2` for our virtual host
    - [ ] We used `AWS VPC` to provision Subnets and our Default Network configuration
    - [ ] We used `AWS IAM` to provision roles and permissions
    - [ ] We used an `AWS AMI` to provision a container optimized EC2 configuration
    - [ ] We used `AWS CloudFormation` to create a IaC template to deploy AWS resources
    - [ ] We have metrics enabled via `AWS CloudWatch` 

3. You learned that the CPU and RAM are shared across the agents deployed to the host. 

4. You learned that when you register an EC2 instance with an ECS Cluster, the cluster automatically creates an autoscaling group that is multi-AZ.

5. If you view the `AutoScaling`, you can see 
    - [ ] That the Launch Configuration specifies the AMI, 
    - [ ] IAM Instance Profile (Permissions), 
    - [ ] The User Data maintains a config file that specifies the cluster allocation, 
    - [ ] Monitoring is enabled 
    - [ ] The ephimeral storage is mounted

6. If you view the `EC2 dashboard` you can see that 
    - [ ] A security group has been provisioned, 
    - [ ] You can config the ingress/egress

7. If you view the `IAM role`, you can see that 
    - [ ] The Policy has been provioned

8. You learend that you can ssh into the EC2 host utilizing your key-pair, and validate that 
    - [ ] You are using an optimized AMI, 
    - [ ] You can view you config file which will indicate to the AMI which ECS cluster to register too

```bash
cat /etc/ecs/ecs.config
```
9. Finally, you learned that the way that the agent knows what cluster to register to is via the Docker image, which is pulled straight from Docker hub, is reading the config file in the `etc` directory. You can see the docker image and associated logs by running the following commands. 

```bash
docker ps
docker logs <image id>
```

![image](https://user-images.githubusercontent.com/8760590/106009982-349c5480-6076-11eb-81e6-253c36028fa3.png)
-----

### How is this a benefit to our customers:
1. Speed to market. The ECS cluster utilize CloudFormation templates to deploy the appropriate resources based on the configuration you specify. These CF templates are IaC that can be version controlled and push-button deployed. 

2. Cost. The resources within the cluster can be configured per workload to ensure that the resource allocation and sizing are the correct balance performance for you workload. The clusters are IaC and can be deployed to Autoscaling groups which support `scale-to-zero`.

3. Operations. If you have an MSP agreement, our SCTG resourecs are able to rollback and deploy resources via push button deployment. If you don't, the customer can execute push button deployment just as easily. The resources are documented via IaC files, there is opportunity for rollback and alerting if failures. 

4. Performance. Testing of these configurations can quickly be spun up/down so optimizing performance can be quickly executed in dedicated phases. 

5. Reliablity. Rollback, modification, eventing, alerting, all can be integrated into current monitoring and ops. 

6. Security. CF allows for firewalls (security groups), Access Control Lists, and a variety of other security services to control ingress/egress and segmentation of networking to allow least privledge. 


## Lab 2: Task Definitions

When using ECS there are multiple parts to configure. A summary of all the piece parts are depicted here below. In lab 1 you were able to create an ECS cluster, and deploy an `ECS Agent` to an EC2 host. Now we need to configure an web server running Apache to a node on our cluster. How is this done? To do this we need to create a `Task Definition`. 

------- 

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106011291-901b1200-6077-11eb-9eb9-b6b3d875a4cd.png" width="550px">
</p>

-------

#### Intended outcome 

------- 

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106028893-bf3a7f00-6089-11eb-9248-7a04be6dd20e.png" width="550px">
</p>

-------

### Steps: 
1. Nav to ECS Services on AWS console. Select ECS Cluster that we created in Lab 1, `demo-cluster`

2. On the left nav pane, select `Task Definitions`, and select `Create New Task Definition`

3. When redirected, you will be prompted to select `launch type`. There are 2 types of `launch type` options. For purposes of this lab we will select `EC2`, and click `Next Step`
    - [ ] Fargate
    - [ ] EC2

4. When redirected, you will configure the `Task Definition`
    - [ ] __Task Definition Name__: Provide the Task Definition a name. For our example write `my-httpd`
    - [ ] __Task Role__: The container your are provisioning will need an IAM role assigned in order to interact with other AWS resources if necessary. For example write to S3 bucket, or trigger a lambda function, etc. In this example no role is needed so you can leave blank. But recognize this is often a source of troubleshooting... if no role was provisioned the resource cannot communicate with other AWS resources. 
    - [ ] __Network Mode__: ECS wants to know how to start your container. If no input is provided then if Linux container, default will be Bridge mode, and if Windows container image, default will be NAT. Leave default for this demo. 

5. Further down the `Task Defintion` configuration page, is the `Task Execution IAM role`
    - [ ] Leave as default. Creation of the required `ecsTaskExecutionRole` will be handled for you, or you more than likely already provisioned this during Lab 1, with the ECS Cluster assignment tasks. 

6. Further down the `Task Definition` configuration page, is the `Task Size`. Recall that when we provisioned the EC2 host, we assigned a EC2 type, `t2.micro`. This host has "X" CPU and "Y" Memory capacity, which we saw in the EC2 details when we provisioned the ECS cluster. (See diagram below). Some portion of the memory capacity was already allocated when we provisioned the `ECS Agent` to the EC2 host, which is why we see `983 MiB` vs. `1 GiB` that a `t2.micro` is initialized with. We will need to once again allocate some portion of CPU and Memory from the host to this `httpd` Task Definition, as the host resources are shared.

    ------
    <p align="center">
    <image src="https://user-images.githubusercontent.com/8760590/106036667-de89da00-6092-11eb-9de1-325318b9cef0.png" width="650px">
    </p>

    ------

    - [ ] __Task Memory (MiB)__: Input `300`
    - [ ] __Task CPU (unit)__: Input `250` 

> NOTE: We will explore how to make an assessment of CPU and Memory at later time, for sake of lab, simply input these values. 

7. Further down the `Task Definition` configuration page, is the `Container Definition` section. 
    - [ ] Click `Add Container`
    - [ ] A UI "drawer" will slide out. 
        - `Container Name`: input `httpd`
        - `Image`: 
            + Here recognize that the image is coming from Docker Hub. If you nav to Docker Hub [here](https://hub.docker.com/) and search for `httpd` you will find a Docker image [httpd](https://hub.docker.com/_/httpd). This is the image we want to specify in the ECS console. 
            + The images are maintained via tagging, the most recent tag will be called `latest` by default. It is often the case that the latest images are not as stable as earlier revs, so for the sake of this example we will use tab `2.4` [here](https://hub.docker.com/layers/httpd/library/httpd/2.4/images/sha256-0936359fc51250267f4d9b8aef7dc238cae1078e478207ab7d6802416494a37e?context=explore)
            + Return from Docker Hub to the ECS console / drawer and in the Image field enter `httpd:2.4`
        - `Private Repository Authentication`: leave unchecked
        - `Memory Limit (MiB)`: Enter 300
        - `Port Mappings`: Host Port = `8080` & Container Port = `80` (see documentation on httpd image)
    - [ ] Scroll past all additional config settings, leaving as default and click `Add`

> NOTE: Within the UI drawer the are several additional configurations that can be modifified 1. Health Check 2. Environment 3. Container Timeouts 4. Network Settings 5. Storage and Loggin 6. Security 7. Resource Limits 8. Docker Labels. No need to worry about any of these additional configurations for this lab. 

8. Upon configuration of the drawer items, you can see that the Container Definition was captured. 

9. There are remaining configuration items within the Task Definition 1. Constraints, 2. Service Integration 3. Proxy Configuration 4. Log Router Integration 5. Volumes & 6. Tags. We will leave these as default for now as these are optional or beyond the scope of this lab. Click `Create`.

10. When redirected, you will be presented with a status bar indiciating `Created Task Definition successfully`. You can see that the name for the Task Definition is `my-httpd:1` indicating the name and version number. 

