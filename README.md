AUTONOMOUS PATCHING WITH EC2 IMAGE BUILDER AND SYSTEMS MANAGER

Introduction

Patching is a vital component to any security strategy which ensures that your compute environments are operating with the latest code revisions available. This in turn means that you are running with the latest security updates for the system, which reduces the potential attack surface of your workload.

The majority of compliance frameworks require evidence of patching strategy or some sort. This means that patching needs to be performed on a regular basis. Depending on the criticality of the workload, the operational overhead will need to be managed in a way that poses minimal impact to the workload’s availability.

Ensuring that you have an automated patching solution, will contribute to building a good security posture, while at the same time reducing the operational overhead, together with allowing traceability that can potentially be useful for future compliance audits.

There are multiple different approaches available to automate operating system patching using a combination of AWS services.

One approach is to utilize a blue/green deployment methodology to build an entirely new Amazon Machine Image (AMI) that contains the latest operating system patch, which can be deployed into the application cluster. This lab will walk you through this approach, utilizing a combination of the following services and features:

1.EC2 Image Builder to automate creation of the AMI.

2.Systems Manager Automated Document to orchestrate the execution.

CloudFormation with AutoScalingReplacingUpdate update policy, to gracefully deploy the newly created AMI into the workload with minimal interruption to the application availability.
We will deploy section 1 and 2 of the lab with CloudFormation templates to get your environment built as efficiently as possible. This will allow the base infrastructure and application deployment to be completed quickly so you can focus on the main lab objectives which are covered in sections 3 and 4. In these sections, we will give you the choice of either using additional pre-built templates or manual steps to complete the EC2 Image Builder and Systems Manager Document configuration.

The skills you learn from this lab will help you secure your workloads in alignment with the AWS Well-Architected Framework.

Goals

1.EC2 Image Builder configuration experience.

2.Systems Manager Automated Document experience.

Steps:

1.Deploy The Lab Base Infrastructure:

In this section, we will build out a Virtual Public Cloud (VPC), together with public and private subnets across two Availability Zones, Internet Gateway and NAT gateway along with the necessary routes from both public and private subnets.

This VPC will become the baseline network architecture within which the application will run. When we successfully complete our initial stage template deployment, our deployed workload should reflect the following diagram:

![image](https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/d6e5dd02-8c51-4c50-99b9-6bfbdb9bb6a4)


To deploy the template for the base infrastructure build follow the approptiate steps:

1.1. Get the Cloudformation Template.
To deploy the first CloudFormation template, you can either deploy directly from the command line or via the console.


 Click here for CloudFormation command-line deployment steps

Command Line Deployment:

To deploy from the command line, ensure that you have installed and configured AWS CLI with the appropriate credentials.

1.1.1. Execute Command

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/824a37bd-c715-4067-805e-e64b6453beae">

Note: Please adjust your command-line if you are using profiles within your aws command line as required.

1.1.2.
Confirm that the stack has installed correctly. You can do this by running the describe-stacks command as follows:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/8308c470-9aee-47bb-8c73-6b56149e6c60">

Locate the StackStatus and confirm it is set to CREATE_COMPLETE as shown here:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/10ea9d17-7a76-4dad-b1c6-7cff950fd3fd">



1.1.3.

Take note of this stack output as we will need it for later sections of the lab.

 1.Click here for CloudFormation console deployment steps

Console:

If you need detailed instructions on how to deploy CloudFormation stacks from within the console, please follow this guide.

Use pattern3-base as the Stack Name, as this is referenced by other stacks later in the lab.

1.2. Note Cloudformation Template Outputs

When the CloudFormation template deployment is completed, note the outputs produced by the newly created stack as these will be required at later points in the lab.

You can do this by clicking on the stack name you just created, and select the Outputs Tab as shown in diagram below.

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/f41c4833-0f36-462a-81ba-c1402bdd32a0">

You can now proceed to Section 2 of the lab where we will build out the application stack.


2.Deploy The Application Infrastructure.

The second section of the lab will build out the sample application stack what will run in the VPC which was build in section 1.

This application stack will comprise of the following :

1.Application Load Balancer (ALB).

2.Autoscaling Group along with it’s Launch Configuration.

Once you completed below steps, you base architecture will be as follows:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/5f1f0960-a4a9-4541-b4be-2c6745e2a1c4">

Building each components in this section manually will take a bit of time, and because our objective in this lab is to show you how to automate patching through AMI build and deployment. To save time, we have created a cloudformation template that you can deploy to expedite the process.

Please follow the steps below to do so :

2.1. Get the Cloudformation Template.

To deploy the second CloudFormation template, you can either deploy directly from the command line or via the console.

 Click here for CloudFormation command line deployment steps

Command Line:

2.1.1.
To deploy from the command line, ensure that you have installed and configured AWS CLI with the appropriate credentials.

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/d71c6061-3c48-4b48-a5ed-ab54f7281893">

Important Note:

For simplicity, we have used Sydney ‘ap-southeast-2’ as the default region for this lab.

We have also pre configured the Golden Amazon Machine Image Id to be the AMI id of Amazon Linux 2 AMI (HVM) in Sydney region ami-0f96495a064477ffb. If you choose to to use a different region, please change the AMI Id accordingly for your region.

Click here for CloudFormation console deployment steps:

2.2. Confirm Successful Application Installation

Once the stack creation is complete, let’s check that the application deployment has been successful. To do this follow below steps:

1.Go to the Outputs section of the cloudformation stack you just deployed.

2.Note the value of OutputPattern3ALBDNSName and you can find the DNS name as per screen shot below:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/c9703b12-b9c2-4f12-8628-e054f0184897">

3.Copy the value and paste it into a web browser.

4.If you have configured everything correctly, you should be able to view a webpage with ‘Welcome to Re:Invent 2020 The Well Architected Way’ as the page title.

5.Adding /details.php to the end of your DNS address will list the packages currently available, together with the AMI which has been used to create the instance as follows:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/10d21e1d-084d-4c24-9dda-9dc7106045a4">

6.Take note of the installed packages and AMI Id (Copy and paste this elsewhere we will use this to confirm the changes later).

7.When you have confirmed that the application deployment was successful, move to section 3 which will deploy your AMI Builder Pipeline.


3.Deploy The AMI Builder Pipeline.

In this section we will be building our Amazon Machine Image Pipeline leveraging EC2 Image Builder service. EC2 Image Builder is a service that simplifies the creation, maintenance, validation, sharing, and deployment of Linux or Windows Server images for use with Amazon EC2 and on-premises. Using this service, eliminates the automation heavy lifting you have to build in order to streamline the build and management of your Amazon Machine Image.

Upon completion of this section we will have an Image builder pipeline that will be responsible for taking a golden AMI Image, and produce a newly patched Amazon Machine Image, ready to be deployed to our application cluster, replacing the outdated one.

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/5a0a8a02-c5ad-4700-af84-7029ca2cc4ba">

In this section you have the option to build the pipeline manually using AWS console, or if you are keen to complete the lab quickly, you can simply deploy from the cloudformation template.

 1.Click here to build your pipeline using CloudFormation on the command line

3.1. Download The Cloudformation Template.

Download the template here.

3.2. Deploy Using The Command Line.

Command Line:

To deploy from the command line, ensure that you have installed and configured AWS CLI with the appropriate credentials:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/cb130555-fef1-4365-b63a-1614c61d9d25">

Note :

1.For simplicity, we have used Sydney ‘ap-southeast-2’ as the default region for this lab.

2.We have also pre-configured the MasterAMI parameter to be the AMI id of Amazon Linux 2 AMI (HVM) in Sydney region ami-0f96495a064477ffb. If you choose to to use a different region, please change the AMI Id accordingly for your region.

 Click here to build your pipeline using CloudFormation through the console
Console:
3.1. Download The Cloudformation Template.
Download the template here.

3.2. Deploy Using The Console.
If you need detailed instructions on how to deploy CloudFormation stacks from within the console, please follow this guide.

1.Use pattern3-pipeline as the Stack Name.

2.Provide the name of the vpc cloudformation stack you create in section 1 ( we used pattern3-base as default ) as the BaselineVpcStack parameter value.

3.Use the AMI Id of Amazon Linux 2 AMI (HVM) as the MasterAMI parameter value. ( In Sydney region ami-0f96495a064477ffb if you choose to to use a different region, please change the AMI Id accordingly for your region. )

3.3. Take note of the ARN.

1.When the CloudFormation template deployment is completed, note the output produced by the stack.

2.You can do this by clicking on the stack name you just created, and select the ‘Outputs Tab’ as shown in diagram below.

3.Please take note of the Pipeline ARN specified under Pattern3ImagePipeline output.

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/0584f75f-4c59-4fc7-99a2-a60eed27fdc2">

4.DEPLOY THE BUILD AUTOMATION WITH SSM

Now that our AMI Builder Pipeline is built, we can now work on the final automation stage with Systems Manager.

In this section we will orchestrate the build of a newly patched AMI and its associated deployment into our application cluster.

To automate this activities we will leverage AWS Systems Manager Automation Document.

Using our SSM Automation document we will execute the following activities:

1.Automate the execution of the EC2 Image Builder Pipeline.

2.Wait for the pipeline to complete the build, and capture the newly created AMI with updated OS patch.

3.Then it will Update the CloudFormation application stack with the new patched Amazon Machine Image.

4.This AMI update to the stack will in turn trigger the CloudFormation AutoScalingReplacingUpdate policy to perform a simple equivalent of a blue/green deployment of the new Autoscaling group.

Note:

Using this approach, we streamline the creation of our AMI, and at the same time minimize interruption to applications within the environment.

Additionally, by leveraging the automation built in Cloudformation through autoscaling update policy, we reduce the complexity associated with building out a blue/green deployment structure manually. Lets look at how this works in detail:


1.Firstly, CloudFormation detects the need to update the LaunchConfiguration with a new Amazon Machine Image.

2.Then, CloudFormation will launch a new AutoScalingGroup, along with it’s compute resource (EC2 Instance) with the newly patched AMI.

3.CloudFormation will then wait until all instances are detected healthy by the Load balancer, before terminating the old AutoScaling Group, ultimately achieving a blue/green model of deployment.

4.Should the new compute resource failed to deploy, cloudformation will rollback and keep the old compute resource running.

For details about how this is implemented in the CloudFormation template, please review the pattern3-application.yml template deployed in section 2.

Once we complete this section our architecture will reflect the following diagram:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/6cbbca3a-4c9d-48d7-9df0-982cc4db7638">

In this section you have the option to build the resources manually using AWS console. If however you are keen to complete the lab quickly, you can simply deploy from the CloudFormation template and take a look at the deployed architecture. Select the appropriate section:

 Build with a CloudFormation template on the command-line
 
4.1. Get The Template

The template for Section 4 which can be found here.


4.2. Deploy From The Command Line

To deploy from the command line, ensure that you have installed and configured AWS CLI with the appropriate credentials. When you are ready, execute the following command:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/310c2fe8-5eed-43b6-aed5-9122f72ec0c4">

4.3. Record The CloudFormation Output.
Once the template is finished execution, note the Automation Document Name from the Cloudformation output specified under Pattern3CreateImageOutput.

 Build with a CloudFormation template in the console
4.1. Get The Template
The template for Section 4 which can be found here.

4.2. Deploy From The Console

To deploy the template from console please follow this guide for information on how to deploy the cloudformation template.

1.Use pattern3-automate as the Stack Name.

2.Provide the ARN of the pipeline you created in section 3.2.6 as ImageBuilderPipelineARN parameter value.

3.Provide the cloudfromation stack name you created in section 2.1 as ApplicationStack parameter value.

4.3. Record The CloudFormation Output.

Once the template is finished execution, note the Automation Document Name from the Cloudformation output specified under Pattern3CreateImageOutput.

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/a272c12f-30f2-4e26-898e-ae4087b904dc">

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/0321deda-382c-49fe-8647-0394a9f26ebe">
