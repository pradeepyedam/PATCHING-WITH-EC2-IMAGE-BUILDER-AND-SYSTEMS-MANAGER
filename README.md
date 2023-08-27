AUTONOMOUS PATCHING WITH EC2 IMAGE BUILDER AND SYSTEMS MANAGER

Authors

1.Tim Robinson, Well-Architected Geo Solutions Architect.

2.Stephen Salim, Well-Architected Geo Solutions Architect.

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

Adding /details.php to the end of your DNS address will list the packages currently available, together with the AMI which has been used to create the instance as follows:

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/10d21e1d-084d-4c24-9dda-9dc7106045a4">
