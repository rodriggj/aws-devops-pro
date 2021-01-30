# Elastic Container Service (ECS)

When using ECS there are multiple parts to configure. A summary of all the piece parts are depicted here below. The lab construct attempts to walk through these various pieces as an individual lab. 

------- 

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106011291-901b1200-6077-11eb-9eb9-b6b3d875a4cd.png" width="450px">
</p>

-------

In order to explore these pieceparts, there are 4 labs below: 
1. Lab 1: Cluster Creation
2. Lab 2: Task Definition
3. Lab 3: Service Creation
4. Lab 4: Load Balancing

------- 

## Lab 1: Cluster Creation

### Intended Outcome 

The intend outcome of this lab is demonstrate how to complete an initial `ECS cluster`, and how various components of the AWS stack enable cluster creation. 

`ECS Cluster` are logical groups of EC2 instances. They are the virtual hosts that will allocate resources to Container images operating on them. Because workloads may need multiple EC2 instances, there needs to be a way to associate EC2 instances into a group or `cluster`. How is this done? At creation, the EC2 instance is built with an optimized Amazon Machine Image (AMI) which at creation will instantiate a small Linux contianer, called the `ECS Agent` that registers the EC2 instance with a ECS Cluster. With multiple EC2s provisioned into an ECS Cluster, the cluster can be deployed to your Virtual Private Cloud (VPC) with all the standard components that are native to a VPC. To visualize this narrative see diagram below. 

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106005913-f1d87d80-6071-11eb-9777-9f02d7e47f1d.png" width="450px">
</p>

------

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

14. Lab complete. Congratulations!

## Lab 2: Task Definitions

In lab 1 you were able to create an ECS cluster, and deploy an `ECS Agent`to an EC2 host. Now we need to configure a web server running Apache to a node in our cluster. We will need to configure `PORT Mappings` and a pull from Docker Hub to obtain the container image. How is this done? The first part of this process is we need to create a `Task Definition`. 

A `Task Defintion` is metadata presented in a JSON format that will tell ECS how to run a Docker Container. It contains criticial information such as 1. Docker Image 2. Port Binding 3. Provisions Memory & CPU 4. Sets ENV variables, and 5. networking information. In this lab we will 1. pull a `httpd` Docker image from Docker hub, 2. deploy the `httpd` container to our EC2 host 3. configure `PORT Mappings` from the EC2 host to the `httpd` container. 4. validate that the configuration is captured in a JSON document.

#### Intended outcome 

------- 

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106045881-c7e98000-609e-11eb-8e6f-f14b65dc4c54.png" width="450px">
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
    <image src="https://user-images.githubusercontent.com/8760590/106036667-de89da00-6092-11eb-9de1-325318b9cef0.png" width="450px">
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
            - [ ] Here recognize that the image is coming from Docker Hub. If you nav to Docker Hub [here](https://hub.docker.com/) and search for `httpd` you will find a Docker image [httpd](https://hub.docker.com/_/httpd). This is the image we want to specify in the ECS console. 
            - [ ] The images are maintained via tagging, the most recent tag will be called `latest` by default. It is often the case that the latest images are not as stable as earlier revs, so for the sake of this example we will use tab `2.4` [here](https://hub.docker.com/layers/httpd/library/httpd/2.4/images/sha256-0936359fc51250267f4d9b8aef7dc238cae1078e478207ab7d6802416494a37e?context=explore)
            - [ ] Return from Docker Hub to the ECS console / drawer and in the Image field enter `httpd:2.4`
        - `Private Repository Authentication`: leave unchecked
        - `Memory Limit (MiB)`: Enter 300
        - `Port Mappings`: Host Port = `8080` & Container Port = `80` (see documentation on httpd image)
    - [ ] Scroll past all additional config settings, leaving as default and click `Add`

> NOTE: Within the UI drawer the are several additional configurations that can be modifified 1. Health Check 2. Environment 3. Container Timeouts 4. Network Settings 5. Storage and Loggin 6. Security 7. Resource Limits 8. Docker Labels. No need to worry about any of these additional configurations for this lab. 

8. Upon configuration of the drawer items, you can see that the Container Definition was captured. 

9. There are remaining configuration items within the Task Definition 1. Constraints, 2. Service Integration 3. Proxy Configuration 4. Log Router Integration 5. Volumes & 6. Tags. We will leave these as default for now as these are optional or beyond the scope of this lab. Click `Create`.

10. When redirected, you will be presented with a status bar indiciating `Created Task Definition successfully`. You can see that the name for the Task Definition is `my-httpd:1` indicating the name and version number. 

11. Lab complete. Congratulations!

------ 

## Lab 3: Service Creation

In Lab 1 you were able to create an ECS `Cluster`, in Lab 2 you were able to configure a `Task Definition` which effectively was a process to create a JSON manifest file that indiciated the configuration of the container you want to run in your `Cluster`. Now in Lab 3 we will configure a file that will tell ECS how many of the `Task Definition` instances that you want running in your cluster. This process is referred to as an `ECS Service`. 

The `ECS Service` will ensure that the number of tasks desired is running across your fleet of EC2 instances in your ECS Cluster. The ECS Service can be linked to your load balancing resources (ELB, ALB, NLB) within your VPC. 

### Steps: 

1. Nav to the ECS Service on you AWS console, and nav to your Cluster `cluster-demo`. Select the hyperlink on `cluster-demo`.

2. When redirected, Click the `Services` tab and click `Create`.

3. When redirected, you will need to `Configure Service`
    - [ ] __Launch Type__: Select `EC2`
    - [ ] __Task Definition__: 
        + `Family`: Should be poplulated
        + `Revision`: Should be populated
    - [ ] __Cluster__: Should be populated
    - [ ] __Service Name__: Enter `httpd-service`
    - [ ] __Service Type__: 
        + Replica: If you select Replica, you need to provision the `Number of Tasks` field with the qty of Task Definitios to create. For the sake of this demo choose `Replica` and provision `1` in the `Number of Tasks` field
        + Daemon: This is a background running process, and if selected, the `Number of Tasks` field auto populates to automatic
    - [ ] __Minimum Healthy Percent__: Enter `0`
    - [ ] __Maximum Percent__: Enter `200`
    - [ ] __Deployments__: 
        + `Rolling Update`: This is the legacy option. For the sake of this lab, leave this selected for now. 
        + `Blue/green deployment`: This is a new feature powered by Code Deploy. 
    - [ ] __Task Placement__: 
        + `Placement Templates`: This dropdown provides selection options for how to distribute tasks on your ECS cluster. For the sake of this lab select, `AZ Balanced Spread`.
    - [ ] __Tagging__: Here you can assign tags to the Service. For the sake of this lab, leave default, and click `Next`.

4. Whe redirected, you will need to `Configure Network`
    - [ ] `Load Balancing`: Here you can select your load balancing service to your cluster. For the purpose of this lab we will leave blank for now, but we will configure an ELB later.
        + Elastic Load Balancer
        + Network Load Balancer
        + Application Load Balancer
    - [ ] `App Mesh`: Here you can leverage a Network service AWS manages, but you will need to make your Service discoverable to enable `App Mesh` option. For the purpose fo this lab, we will leave this `unchecked` and not apply a Service Mesh. 
    - [ ] `Service Discovery`: Within the ECS Cluster you can enable Service Discovery. We will leave off for purpose of this demo. 
    - [ ] Click `Next Step`

5. When redirected, you will be able to configure `Set Auto Scaling`. 
    - [ ] You can either enable or disable autoscaling. The auto scaling is enabled via CloudWatch alarms. For purposes of this lab we will leave `disabled`.

6. When redirected, you will be able to `Review` your configration. 
    - [ ] Click `Create Service`

7. When redirected, you will see a status bar indicating `Launch Status`. From this page you can see there is an integration available to `Code Pipeline` so you can integrate your CI/CD process directly to your `httpd` service. For purposes of this lab this is not needed.  Click `View Service`

8. When redirected, you will be back at the ECS landing page, for the `httpd-service`. Here you can once again see a table of various views that will provide status of the `httpd` service deployed to your ECS cluster. 

9. Now we can attempt to see our service running. If you view the ECS Cluster Dashboard page, and view the Status table you can see that the `Task Definition` for the __httpd__ `Service` is now __Running__. (See screenshot below)

    ------

    <p align="center">
    <image src="https://user-images.githubusercontent.com/8760590/106181944-73580a80-615b-11eb-8b66-04340bfc6252.png" width="450px">
    </p>

    ------

10. So now lets `ssh` into our ECS Cluster (the EC2 host) and view the running containers. 1. On your laptop, nav to the directory location of your `key-pair`, 2. open a terminal window 3. execute the following command to ssh into your EC2 instance

```bash
ssh -i "us-east.pem" ec2-user@ec2-3-219-233-224.compute-1.amazonaws.com
```

11. Once connected to your EC2 instance run 

```bash
docker ps
```

- [ ] Here you should see a terminal output that looks like this ... where you can see 2 docker images running on your EC2 host. 

    ------

    <p align="center">
    <image src="https://user-images.githubusercontent.com/8760590/106184215-77395c00-615e-11eb-82cc-d05bc15a77aa.png" width="450px">
    </p>

    ------

12. Now that we see the service is deployed, validated by both the AWS console, and on the EC2 instance itself. In theory we should be able to now reach the httpd service via the port mappting we configured. Recall in our Task Definition we configured our port mapping to be `8080:80` (host:container). So we should be able to send an `http` request to our host via port 8080 and see a response... correct? Let's try ... go to a browser and type in the URL path ... `3.219.233.224:8080` ... 

    ------

    <p align="center">
    <image src="https://user-images.githubusercontent.com/8760590/106185325-e8c5da00-615f-11eb-8de1-3c9f81c75a19.png" width="450px">
    </p>

    ------

> RESULT: The page never resolves?! It simply hangs and ultimately the request times out. Why is that? 

13. Recall from our Lab 1. That we configured our EC2 host to have a security group that only allows inbound traffic to communicate with the EC2 instance via `port 22 (SSH)`. 

We are now trying to communicate with the host via some `port 8080` and subquently ECS will forward the request from the host to the `httpd` service via the port mapping we set up in our `Task Definition` to `port 80`. But there is no PORT open on our Security Group for `8080`. Therefore our EC2 does not receive the request, and the browser hangs and ultimately timesout. 

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106187690-2e37d680-6163-11eb-8886-aff01ec85197.png" width="450px">
</p>

------

14. To fix this, we need to open port 8080 to our EC2 host. 
    - [ ] Nav to our EC2 Service on the AWS Console
    - [ ] Select the Security Groups hyperlink on the left nav
    - [ ] Select the security group associated with our ECS Cluster. Click `Edit Inbound Rules`
    - [ ] When redirected, click `Add Rule`
        + `Type`: Select `Custom TCP`
        + `Port Range`: Type 8080
        + `Source`: Anywhere, and click the `0.0.0.0/0`
        + `Description` Type httpd
        + Click `Save Rules`

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106189558-b6b77680-6165-11eb-8700-4e64e8143909.png" width="450px">
</p>

------
    
15. Now give your `http` request another try. Pull up a browser, and type into the URL `http://3.219.233.224:8080/`

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106189941-2af21a00-6166-11eb-84d3-2dad50a48e2a.png" width="450px">
</p>

------

16. Now that we can get communiction to our `httpd` service, how do we address scaling. Can we add additional docker images to handle scaling concerns. The answer is definitley __yes__ we can scale the service. 
    - [ ] Click `Clusters`
    - [ ] Click `httpd-service`
    - [ ] Click `Update`
    - [ ] On `Number of Tasks` field increase the qty from 1 to 2
    - [ ] Click `Next Step`
    - [ ] Click `Next Step`
    - [ ] Click `Next Step`
    - [ ] Click `Update Service`
    - [ ] Click `View Service`

So now that we have another service running, we should see this on our host correct? Go see ...

```bash
docker ps
```

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106192861-f8e2b700-6169-11eb-8a26-e8de292f6f63.png" width="450px">
</p>

------

Still only the Agent and the original service instance!? 

17. So what happened? You __CANNOT__ have 2 services running on the same port. If you look at the Event log on your service you will see a similar message. So what do we need to do to ensure that ECS can manage the port mappings for our services so we can scale our service without running into PORT conflicts? We can sale our EC2 instances. 

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106193132-56770380-616a-11eb-9de9-6ee219453e61.png" width="450px">
</p>

------

> NOTE: This solution isn't ideal. We don't want to simply add virtual hosts to maintain a 1:1 mapping of service to host. In later labs we will resolve this via other techniques. For now, recognize that you can scale the EC2 instances and deploy a replica of our service via the Task Definition JSON file. 

18. To increase the cluster execute the following: 
    - [ ] Click `Clusters`
    - [ ] Click `ECS Instances`

> NOTE: If you don't see the "Scale ECS Instances" button, go to the underlying Auto Scaling Group (in the EC2 console) and set the desired capacity in the ASG to `desired capacity` and `max capacity` to 2.


We have now created a second EC2 Host, utilized our Task Definition to deploy our Containers, and enabled our `httpd` service

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106196207-609b0100-616e-11eb-8bf8-d18deed66053.png" width="450px">
</p>

------

Now we have a final configuration that looks like this from a topology standpoint

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106195587-94295b80-616d-11eb-910e-a2816986e9f3.png" width="450px">
</p>

------

We can validate this topology by `ssh`-ing to our EC2 instances and validating that we have 2 like images on 2 seperate hosts. 

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106197244-b328ed00-616f-11eb-98ff-de5779f9dfba.png" width="450px">
</p>

------

Finally we can validate via the browser 

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106197857-685ba500-6170-11eb-8fff-c25af476509c.png" width="450px">
</p>

------

19. Lab 3 Complete. Congratulations. 

------

### Lab 4 - Load Balancing

In Lab 3, you saw that to address scale it is possible to add to the cluster an additional EC2 host, to the ECS cluster, and allow the ECS Service to use the AutoScaling policy to balance EC2 host deployment across Availability Zones. This netted in 2 EC2 hosts 1 in AZ 1a & 1 in Az 1b. This was required because our Task Definition specified a particular port mapping (8080:80) to route http traffic to our `httpd` contiainer. This is an inefficient use of EC2 instances. 

In this lab we will see that we do not need to specify host port definition in our `Task Definition`. Instead we can use `Dynamic Port Mapping` that the ECS Service provides. By excluding a host PORT mapping that we bind to the container, and allow ECS to dynamically allocate a host port that binds to the container port, we overcome PORT conflicts and multiple containers can be provisioned on a single EC2 host. (see diagram below).

We will solve this problem in 3 steps: 
1. Configure our Service to allow for dynamic port mapping
2. We will open a security group to allow ingress to our EC2 host on a port range, since they will now be dynamically allocated
3. We will load balance the inbound traffic across all the Services we have running

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106363251-aa0d5c80-62e4-11eb-8eb6-63c89814f2c7.png" width="450px">
</p>

------

### Steps

#### Part 1: Enable Dynamic Port Mappings

1. Nav to AWS ECS Service
2. Select you `Service` Tab, and select `httpd-service` service, select `Update`
3. When redirected we change the `Number of Tasks` from 2 to 4. Click `Next Step` x 3, and click `Update Service`
4. On the `Deployments` tab we still only see 2 services deployed, and if we look on the `Events` tab we see why, we still have a port mapping confict. 

> NOTE: If you view your `Deployment` Tab, see that there are still only 2 services running, not 4 as was just configured. You will recall this is b/c there is a port conflict. The `Task Definition` for the `httpd-service` is specifing a port mapping from the host to the container. Since only 1 port on the host can contain only 1 mapped container, we had to create a 2nd EC2 instance. But now we are trying to create 4 container instances on the 2 EC2 hosts. So AWS ECS is showing you in the deployment screen that it still will only deploy 2 services b/c of the port conflict issue. 

5. To fix this we need to create a new Task Definition. 
    - [ ] Click on `Task Definition`
    - [ ] Click on the existing task, `my-httpd`
    - [ ] Click `Create New Revision`
    - [ ] Leave all configuration items the same, __except for `Container Definition`__
        + Click on `httpd`
        + When the drawer renders, scroll down to `Port Mappings` and remove the host port reference to `8080`
        + Click `Update` and the drawer should close.
    - [ ] Scroll to bottom of `Task Definition` page and click `Create`. 
    - [ ] When redirected you should see a green status bar indiciating a new verision of the `Task Definition` has been created successfully.

> NOTE: If you view the json file for the Task definition you can see that the host port has not been specified for our Task Definition for `my-httpd:2`, whereas in Task Definition for `my-httpd:1`, we specified the host port as `8080`.

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106361127-81339a00-62d9-11eb-9501-667cd160c684.png" width="450px">
</p>

------

6. We now need to update our `Service` to use this new `Task Definition`. 
    - [ ] Click on `Clusters`, and click on `httpd-service`, and click `Update`
    - [ ] When redircted, find `Task Definition` and we will update the revision from 1 to 2 (latest). 
    - [ ] Click through all the redircted screens, with `Next Step`
    - [ ] Click `Update Service`, Click `View Service`

7. Now you can see on the Deployment Tab, there are 2 deployments. 
    1. The original deployment of 2 EC2 hosts with 1. an agent and 2. a httpd service container deployed, balance across 2 AZs. This is listed in the Deployment Tab as `Active`. 
    2. Here we have a new deployment. You can see that there are 0 `Running Count` of instances. This is referred to as the `Primary`. 

8. As ECS quickly spins up the Service, you will see the `Active` deployment go away, as it is now replaced by the `Primary`. You can see that the 4 container instances have now been provisioned and are up and running. 

9. If you `ssh` into the instances, you can now see 2 services running on your EC2 host, and you can see that the port mappings have been dynamically allocated. 

------

<p align="center">
<image src="https://user-images.githubusercontent.com/8760590/106362707-7e3ca780-62e1-11eb-9763-ab8cc6e8d578.png" width="450px">
</p>

------

#### Part 2: Configure the security group to account for dynamic port allocation

13. So we've fixed part of our problem. We can now get multiple containers on a single EC2 host without the issue of port mapping conflicts. But the problem we now have is the user experience. The user is not going to know what port the container was mapped too, nor will we. Therefore we need to set up 2 more things 
    1. We need to configure a security group that will allow ingress http traffic from/to a dynamic port range
    2. We need to load balance the inbound traffic to the 4 instances we have running

#### Part 3: Load balance inbound requests to the httpd service