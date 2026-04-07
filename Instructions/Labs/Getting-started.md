# Deploy a Chat Application with Azure OpenAI and Container Apps
### Overall Estimated Duration: 4 Hours

---

## Overview

In this hands-on lab, you will build and deploy a production-ready AI-powered chat application on Microsoft Azure. You will work with Azure OpenAI Service, Azure Container Apps, Azure Container Registry, and Log Analytics Workspace to create a fully functional chat interface powered by GPT-4o-mini.

The lab takes you through the complete deployment lifecycle: creating an Azure OpenAI resource and deploying a language model, connecting to Azure CLI and configuring the development environment, deploying cloud infrastructure using Bicep as code, building and pushing a Docker image to Azure Container Registry, configuring secure managed identity access, and finally testing the live chat application and monitoring its logs using Log Analytics Workspace.

---

## Objectives

By the end of this lab, you will be able to:

- Create an Azure OpenAI resource and deploy a GPT-4o-mini language model through Azure AI Foundry.
- Connect to Azure CLI from Cloud Shell, clone the project repository, and configure environment variables.
- Deploy Azure infrastructure including Container Apps Environment, Container Registry, and Managed Identity using a Bicep template.
- Build and push a Docker image to Azure Container Registry using ACR Build without any local Docker installation.
- Configure secure passwordless access using Azure RBAC role assignments on a Managed Identity.
- Test the live AI-powered chat application and monitor application logs using Log Analytics Workspace and KQL queries.

---

# Getting Started with the Lab

Welcome to your Deploy a Chat Application with Azure OpenAI and Container Apps lab! We have prepared a seamless environment for you to explore and learn about Azure OpenAI, containerized deployments, and cloud infrastructure as code. Let's begin by making the most of this experience.

## Accessing Your Lab Environment

Once you are ready to dive in, your virtual machine and **Guide** will be right at your fingertips within your web browser.

![Access Your VM and Lab Guide](../media/gs-1.png)

## Lab Guide Zoom In/Zoom Out

To adjust the zoom level for the environment page, click the **A↕** icon located next to the timer in the lab environment.

![](../media/gs-2.png)

## Virtual Machine and Lab Guide

Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success.

## Exploring Your Lab Resources

To get a better understanding of your lab resources and credentials, navigate to the **Environment** tab.

![Explore Lab Resources](../media/gs-3.png)

## Utilizing the Split Window Feature

For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the top right corner.

![Use the Split Window Feature](../media/gs-4.png)

## Managing Your Virtual Machine

Feel free to **Start, Stop, or Restart (2)** your virtual machine as needed from the **Resources (1)** tab. Your experience is in your hands!

![Manage Your Virtual Machine](../media/gs-5.png)

## Let's Get Started with Azure Portal

1. On your virtual machine, click on the Azure Portal icon as shown below:

   ![Launch Azure Portal](../media/gs-6.png)

1. You'll see the **Sign into Microsoft Azure** tab. Here, enter your credentials and click **Next (2)**

   - **Email/Username:** <inject key="AzureAdUserEmail"></inject> **(1)**

   ![Enter Your Username](../media/gs-7.png)

1. Next, provide your password and click on **Sign in (2)**

   - **Password:** <inject key="AzureAdUserPassword"></inject> **(1)**

   ![Enter Your Password](../media/gs-8.png)

1. If prompted to stay signed in, you can click **No**.

   ![Stay Signed In Prompt](../media/gs-9.png)

1. You will now be directed to the Azure Portal home page. You are now ready to begin the lab.


   > **Note:** Use the ODL credentials provided above for all Azure portal and Cloud Shell authentication throughout this lab. Do not use any personal Microsoft accounts.

---

## Lab Guide Navigation

This lab is divided into four labs. Here is a quick overview of what each lab covers:

| Lab | Title | Duration |
|-----|-------|----------|
| Lab 01 | Get Started with Azure OpenAI Service | 40 minutes |
| Lab 02 | Azure Integration and Environment Configuration | 40 minutes |
|  | Break | 20 minutes | 
| Lab 03 | Deploy Azure Resources using Bicep | 120 minutes |
| Lab 04 | Monitor the Chat Application using Log Analytics Workspace | 30 minutes |

> **Note:** Each lab builds on the previous one. Complete them in order. Do not skip any lab as the resources created in earlier labs are required in later labs.

---

## Important Notes Before You Begin

> **Cloud Shell:** All command-line steps in this lab use **Azure Cloud Shell in PowerShell mode**. Do not use Bash. Do not use a local terminal. Always run commands in Cloud Shell accessed from the Azure Portal or shell.azure.com.

> **Credentials:** Never use personal accounts. Always use the ODL credentials provided in the Environment tab.

> **Notepad:** Keep a Notepad window open throughout the lab. You will need to save values such as your OpenAI endpoint, deployment name, registry name, and container app name as you progress through the labs.

> **Timeouts:** If Cloud Shell times out during execution, run `az login` again, authenticate using the device code link, and select the correct subscription before continuing.

---

## Support Contact

The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored for both learners and instructors.

Learner Support Contacts:

- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Click on **Next** from the lower right corner to move on to the first lab.

![Start Your Azure Journey](../media/gs-10.png)

## Happy Learning!!