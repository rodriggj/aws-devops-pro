# CloudFormation 

## Lab 1: Create first cloud formation template
Steps: 
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

6. At the next from, enter `Stack Name`. For this example enter `my-first-cf-template`. Select `Next`. 
7. At the next from you can enter a series of configuration items to include: 
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

