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

You can get the template here.

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

 Click here for CloudFormation console deployment steps

Console:

If you need detailed instructions on how to deploy CloudFormation stacks from within the console, please follow this guide.

Use pattern3-base as the Stack Name, as this is referenced by other stacks later in the lab.

1.2. Note Cloudformation Template Outputs

When the CloudFormation template deployment is completed, note the outputs produced by the newly created stack as these will be required at later points in the lab.

You can do this by clicking on the stack name you just created, and select the Outputs Tab as shown in diagram below.

<img width="960" alt="image" src="https://github.com/pradeepyedam/PATCHING-WITH-EC2-IMAGE-BUILDER-AND-SYSTEMS-MANAGER/assets/134625420/f41c4833-0f36-462a-81ba-c1402bdd32a0">

