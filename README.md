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

