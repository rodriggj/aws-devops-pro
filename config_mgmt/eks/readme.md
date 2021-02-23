# Elastic Kubernetes Serivce (EKS)

## How EKS Works

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108800453-64912780-7550-11eb-9701-fe18c73419b4.png" width=600 height=auto>
</p>


## Pre-reqs to working with EKS
__Kubernetes__
- [ ] Kubernetes API Server
- [ ] Kublet
- [ ] Master & Worker Nodes, Pods
- [ ] Deployments, Services
- [ ] Networking with Kubernetes

### Cluster

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108801072-f8172800-7551-11eb-8d0a-9084675472c7.png" width=300 height=auto>
</p>

1. The `master` manages the cluster. The master coordinates all activities in your cluster, such as scheduling applications, maintaining applications' desired state, scaling applications, and rolling out new updates.

2. A node is a VM or a physical computer that serves as a worker machine in a Kubernetes cluster.

3. Each node has a `Kubelet`, which is an agent for managing the node and communicating with the Kubernetes master. 

4. The node should also have tools for handling container operations, such as `containerd` or `Docker`.

5. When you deploy applications on Kubernetes, you tell the `master` to start the application containers. 

6. The master schedules the containers to run on the cluster's nodes. The nodes communicate with the master using the Kubernetes API, which the master exposes. End users can also use the Kubernetes API directly to interact with the cluster.

### Deployment

7. Once you have a running Kubernetes cluster, you can deploy your containerized applications on top of it. To do so, you create a Kubernetes `Deployment` configuration. You can create and manage a Deployment by using the Kubernetes command line interface, `Kubectl`.

8. Once the `Deployment` is complete the Kubernetes master schedules the application instances included in that Deployment to run on individual Nodes in the cluster. 

9. a Kubernetes `Deployment Controller` continuously monitors those instances. If the Node hosting an instance goes down or is deleted, the Deployment controller replaces the instance with an instance on another Node in the cluster. 

10. When you create a `Deployment`, you'll need to specify the container image for your application and the number of replicas that you want to run. You can change these as needed. 

11. When you create a `Deployment` Kubernetes created a Pod to host your application instance. A `Pod` is a group of one or more application containers (such as Docker) and includes shared storage (volumes), IP address and information about how to run them. A Pod models an application-specific "logical host" and can contain different application containers which are relatively tightly coupled.

### Pods

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108801637-7d4f0c80-7553-11eb-9288-569a0faf7074.png" width=300 height=auto>
</p>

12. A Pod always runs on a `Node`. A Node is a worker machine in Kubernetes. A Node can have multiple pods, and the Kubernetes master automatically handles scheduling the pods across the Nodes in the cluster. Every Kubernetes Node runs at least: 
    1. Kubelet, a process responsible for communication between the Kubernetes Master and the Node; it manages the Pods and the containers running on a machine.
    2. A container runtime (like Docker) responsible for pulling the container image from a registry, unpacking the container, and running the application.

### Nodes 

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108801980-64932680-7554-11eb-8ebd-94d6c565a320.png" width=300 height=auto>
</p>

13. Kubernetes Pods are mortal. Pods in fact have a lifecycle. When a worker node dies, the Pods running on the Node are also lost. A `ReplicaSet` might then dynamically drive the cluster back to desired state via creation of new Pods to keep your application running. 

### Service

14. A Kubernetes `Service` is an abstraction layer which defines a logical set of Pods and enables external traffic exposure, load balancing and service discovery for those Pods.

15. Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic. Services can be exposed in different ways by specifying a type in the ServiceSpec: 1. ClusterIP, 2. NodePort, 3. LoadBalancer, & 4. ExternalName

16. A Service routes traffic across a set of Pods. Discovery and routing among dependent Pods (such as the frontend and backend components in an application) is handled by Kubernetes Services.

17. Services match a set of Pods using labels and selectors, a grouping primitive that allows logical operation on objects in Kubernetes. 

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108802344-4b3eaa00-7555-11eb-9dde-b109bc3ac2d3.png" width=300 height=auto>
</p>

### Scaling 

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108802447-8b9e2800-7555-11eb-9644-285c947ef154.png" width=300 height=auto>
</p>

__AWS__
- [ ] VPC, Subnets
- [ ] IAM 
- [ ] EC2, EBS
- [ ] Load Balancers
- [ ] Security Groups

## What Does EKS do for the User? 

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108799759-57733900-754e-11eb-965b-ec7e60a17657.png" width=600 height=auto>
</p>

--------

### Labs

#### Lab 1: Setup IAM user and permissions to acces EKS resources

Steps:
1. On AWS console nav to `IAM`
2. Click on `Users`
3. If no user exists, create one. If one does exist you want to use, ensure they are provisioned `AdministratorAccess` policy.
4. On IAM console click `Roles` \ `Create Role` \ `EKS` 
5. Under `Select your use case` select `EKS` \ `Next: Permissions`
6. Under `Policy name` leave the default policy `AmazonEKSServiceRolePolicy` \ `Next: Tags` 
7. No need to add any Tags, select `Next: Review` \ Select `Create role`
8. Ensure that you create a `key pair` out of the EC2 console. 

------

#### Lab 2: Setup commandline cli tools

 - `aws cli`: required as dependency of `eksctl` to grab authentication token. Pre-reqs `python` & `python pip`
 - `eksctl`: setup and operation of EKS cluster
 - `kubectl`: interaction with K8's API server

Steps:
1. Follow instructions here for `aws cli` [here](https://docs.aws.amazon.com/cli/latest/userguide/install-macos.html)
2. Configure your `aws profile` via the CLI 

```bash
aws configure
```

>  Provide the `Access Key ID`, `Access Secret`, `Region`, `Output format`. You can validate that the changes were correctly implemented by viewinging the `credentials` file located at `cd ~/.aws`.

3. Download `eksctl` from _weaveworks_ github repository

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
```

4. Now we need to move from `/tmp` folder to `/usr/local/bin`

```bash
sudo mv /tmp/eksctl /usr/local/bin
```

> Validate that the `eksctl` client is installed by running `eksctl version` in the CLI and you should see a version returned.

5. Now you want to install `kubectl`. See Mac OS installation [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

```bash 
brew install kubectl
kubectl version --client
```

-------

#### Lab 3: Create first EKS Cluster

Here we will create an `EKS cluster` configured with the following specifications: 
- [ ] Region: `us-east-1`
- [ ] Nodegroup(s): 1
- [ ] Worker Nodes: 3, `t2.small`
- [ ] Port/Access: `ssh`
- [ ] VPC/AZ: 1 VPC 2 Subnets across 2 AZ's

Steps: 
1. We can create an EKS cluster via the command line with the `eksctl` CLI. 

```bash
eksctl create cluster --help
```

> You can see all the flags and options available to you to create an EKS cluster via CLI. There is an alternative which is to create your EKS cluster via `YAML`

2. To create the same EKS cluster we can alos source the build from an .YAML file. 

```bash
eksctl create cluster -f firstCluster.yaml
```

<p align="center">
<img src="https://user-images.githubusercontent.com/8760590/108922411-5ac10000-75f4-11eb-9f2d-a4b961bc45d8.png" width=600 height=auto>
</p>

-------

### Reference

- [ ] Kubernetes [here](https://kubernetes.io/docs/home/)
- [ ] etcd [here](https://etcd.io/)
- [ ] eksctl [here](https://eksctl.io/)

-------