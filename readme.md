# Elastic Kubernetes Service (EKS)

## What problem does EKS Solve

> Kubernetes is an enterprise grade, orchestration tool for managing container deployments. It is highly popular, efficient, and is gaining tremendous adoption. So much so that IT professionals are finding a need to develop skills to operate and create K8 clusters. Without AWS, IT Professionals who needed K8 clusters had the responsibility to execute the following: 
- [ ] Deploy and manage a Control Plane (aka Master Nodes)
- [ ] Deploy `etcd`
- [ ] Setup Certificate Authorities (CA) aned TLS encryption
- [ ] Setup monitoring / auto scaling / and self-healing
- [ ] Setup setup security, authorization, and authentication 
- [ ] Deploy and manage Worker Nodes 

And this is just `setup`! This same setup needed to be completed for each environment that you needed to build. 


This where Amazon EKS comes in. The EKS service abstracts this setup away so developers can actual manage workloads and deploy features. 

> Amazon EKS will manage the following for you: 

- [ ] Master nodes in High Availability 
- [ ] Etcd ensemble in HA
- [ ] API Server 
- [ ] KubeDNS
- [ ] Scheduler
- [ ] Controller Manager 
- [ ] Cloud Controller

You have to create your worker nodes and deploy your applications. 

--------



