# CloudFormation 

## Lab 1: Create first cloud formation template
### Steps: 
1. First lets build a simple `.yaml` file that will we upload to an S3 bucket that CloudFormation will use for the inital build. 
    + In a code editor create a file called `just_ec2.yaml` file.
    + In the file we will input the following code, and save the file.

    ```yaml
    ---
    Resources: 
        MyInstance: 
            Type: AWS::EC2::Instance
            Properties: 
                AvailabilityZone: us-east-1a
                ImageId: ami-a4c7edb2
                InstanceType: t2.micro
    ```

2. Login to AWS console, and nav to `cloudformation` service
3. Check the region. Nav to `us-east (N.Virgina)` region
4. We will now create a stack. Select `Create a Stack`
5. You will have to select a template design. We will select the `.yaml` file that we created in Step 1. And click `Next`

> NOTE: You have 4 choices to select a template. 1. Use a `Design Template` 2. Select a `Sample Template` 3. Upload a Template to AWS S3, 4. Specify an AWS S3 URL. 

6. At the next form, enter `Stack Name`. For this example enter `my-first-cf-template`. Select `Next`. 
7. At the next form, you can enter a series of configuration items to include: 
    + __Tags__
    + __Permissions__ (implemented by IAM Policies - we do not need to do anything b/c we are already admin)
    + __Rollback Triggers__
    + __Advanced Settings__
        + SNS Topics
        + Termination Protection
        + Timeout
        + Rollback on Failure
        + Stack Policy
8. Leave the configuration parameters as default and on Step 7 and select `Next`
9. At the next redirect you are presented with S3 URL that the template is now hosted and a link for anticipted `cost`, which when clicked will take you to AWS `Simple Monthly Calculator`. You can evaluate any additional options listed in the template and finally click `Create`.
10. The next redirect shows a dashboard for CloudFormation. You can see that the stack is now being created.
11. Click through tabs to see what is avaialble via AWS to display the resulting output. View the implmentation on the EC2 tab, and in the `Cloud Formation Designer`. 

### What did you learn: 
1. You learned that deployments of infrastructure can be automated as Infrastructure as Code (IaC). In this case we created a `.yaml` file, that contains the configuration that Cloud Formation can read to create IaC. 

> NOTE: There are other types of config file formats that will do the same thing as `.yaml`. Examples include Terraform, Ansible, Chef, etc. 

2. You learned that deployment of AWS resources always involves mulitple AWS services that are combined to execute a task. 
    1. `AWS S3` (hosts the config file) 
    2. You saw `AWS EC2` (the deployed resource) 
    3. You saw `AWS Cost Calculator` (shows the anticipated monthly cost) 
    4. You saw `Cloud Formation` and `Designer` (shows the assets being deployed) 
    5. You saw `AWS IAM` (Provisions roles and permissions to allow resources to be created) 

3. You learned that there are ways to leverage reuse in resource deployment. The `.yaml` file can be `configuration controlled` into a `gold copy` that implements standards, best practice, reuse, etc. etc. in an automated way A benefit that doesn't exist in manual on-prem deployments. 

4. You learned that WAF can be applied to the template. Tenants of Cost, Operational Effictivenss, Performance, Reliability, Security can all be found in implementing resources like this. 