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

15. Although each Pod has a unique IP address, those IPs are not exposed outside the cluster without a Service. Services allow your applications to receive traffic. Services can be exposed in different ways by specifying a type in the ServiceSpec:
    1. ClusterIP
    2. NodePort 
    3. LoadBalancer
    4. ExternalName

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