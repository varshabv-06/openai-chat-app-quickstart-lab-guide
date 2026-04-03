# Lab01: Get started with Azure OpenAI Service

Azure OpenAI Service brings the generative AI models developed by OpenAI to the Azure platform, enabling you to develop powerful AI solutions that benefit from the security, scalability, and integration of services provided by the Azure cloud platform. In this exercise, you'll learn how to get started with Azure OpenAI by provisioning the service as an Azure resource and using Azure OpenAI Studio to deploy and explore OpenAI models.


### Estimated Duration:

## Overview 
This lab guides you through building a simple chat application using Azure OpenAI and Python. You will deploy a language model, connect it securely using API credentials, and create a Python-based interface for user interaction. The lab focuses on understanding how Azure OpenAI integrates with applications.

## Objectives

In this lab, you will perform the following:

- Task 1: Create Azure OpenAI Resource
- Task 2: Deploy a Model

### Task 1 - Create Azure OpenAI Resource

In this task, you will create an Azure OpenAI resource, which provides access to powerful AI models like GPT.

1. On your LabVM, open a new browser tab and login to **Azure Portal**, using the below URL: 

   ```
   https://portal.azure.com
   ```

    >**Note:** Use the ODL credentials to login to Azure portal.

   > - Username : **<inject key="AzureAdUserEmail"></inject>**

   > - Password : **<inject key="AzureAdUserPassword"></inject>**

1. In the Search bar of the Azure portal, type **Azure Open (1)**, then select **Azure OpenAI (2)**.
    ![](../media/L1-T1-S2.png)
     Click on + **Create** to create a new workspace and select **Azure OpenAI**.

    ![](../media/Lab1-Task1-Step2.png)

1. Create an **Azure OpenAI** resource with the following settings:
    - **Subscription**: An Azure subscription that has been approved for access to the Azure OpenAI service.
    - **Resource group**: Create a new resource group with a name of your choice.
    - **Region**: Choose any available region.
    - **Name**: A unique name of your choice.
    - **Pricing tier**: Standard S0
    ![](../media/Lab1-Task1-Step3.png)
    ![](../media/Lab1-Task1-Step3.1.png)
     Once it's created, click on **Go to resource**

 ### Task 2 - Deploy a Model

 In this task, you are creating a model deployment inside Azure OpenAI, which acts like a ready-to-use AI endpoint for your application. Instead of calling a generic model, your app will call this specific deployment name. Azure allocates resources and exposes it as an API so your backend can send prompts and receive responses.

1. Navigate to the **Azure AI Foundry portal**, which opens in a separate browser window, allowing you to access and manage AI resources independently from the main Azure portal.

    ![](../media/Lab1-Task2-Step1.png)

    > **Note:** Once navigated, copy your **keys** and **endpoints** and save them securely in Notepad for future use.

1. Navigate to the **Deployments** section and click + **Create Deployment** to start setting up a new model deployment.
    ![](../media/Lab1-Task2-Step2.png)

1. Search for the model **gpt-4o-mini**, then set the Deployment name as **chat-model** while configuring the deployment.
    ![](../media/Lab1-Task2-Step3.png)

    ![](../media/Lab1-Task2-Step3.1.png)

    GPT-4o Mini is a compact version of advanced large language models that provides a balance between performance, speed, and cost. It is optimized for chat-based applications where low latency and scalability are important. The model processes natural language input, understands context, and generates meaningful, coherent responses.

    > **Note:** Copy your **keys** and **endpoints** and save them securely in Notepad for future use.
