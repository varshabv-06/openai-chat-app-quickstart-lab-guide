# Lab02: Azure Integration and Environment Configuration

Connecting to an Azure account using CLI allows users to securely access and manage cloud resources from the command line. Cloning a repository using a Git URL helps in retrieving project code and maintaining version control. A Log Analytics Workspace is used to collect, monitor, and analyze logs, enabling better performance tracking and troubleshooting within the Azure environment.

### Estimated Duration:

## Overview
In this lab, you will cover the initial setup required for the project, including connecting to Azure using CLI and cloning the repository using the Git URL. It also involves configuring the local environment for smooth execution. Additionally, a Log Analytics Workspace is created to enable monitoring and logging. These steps ensure the project is ready for development and deployment.

## Objectives

In this lab, you will perform the following:

- Task 1: Connect Azure Account to CLI
- Task 2: Clone Repository and Configure Environment
- Task 3: Create Log Analytics Workspace

### Task 1 - Connect Azure Account to CLI

In this task, you will connect your Azure account to the Azure CLI to manage cloud resources directly from the command line. This is done by signing in using a command, which authenticates your account and allows you to create, configure, and monitor Azure services programmatically. It simplifies automation and deployment tasks without using the portal.

1. Navigate back to the Azure portal

   https://portal.azure.com
   
   and select **Cloud Shell** from the top menu to open a browser-based command-line interface for managing your resources.

   ![](../media/L2-T1-S1.png)

   ![](../media/L2-T2-S1.1.png)

    Switch to the classic version to ensure compatibility with the lab instructions and sample project. Some features, deployment steps, or configurations—especially related to Azure OpenAI—are easier to locate or behave consistently in the classic interface. This helps avoid confusion and ensures smooth execution of the lab.

1. In the Cloud Shell, run the 

      ```
       az login
      ``` 
      command to initiate authentication. A prompt will appear with a link (https://login.microsoft.com/device); click on this link and open it in your browser. Enter the provided device code to securely sign in to your Azure account. After successful authentication, return to the Cloud Shell and select the required subscription from the list. Once selected, your CLI session will be ready to manage Azure resources.

    ![](../media/L2-T1-S2.1.png)

    Log in using the provided email ID and click on Continue to proceed with the authentication process.

    ![](../media/L2-T1-S2.2.png)

1. Select the provided subscription from the list by entering the corresponding number and pressing Enter to continue with the configuration.

    ![](../media/L2-T1-S2.png)

### Task 2 - Clone Repository and Configure Environment

In this task, you will build the application locally, configure Azure OpenAI connection, and containerize it using Docker.

1. Use the Git URL 

    [OpenAI Chat App Quickstart](https://github.com/varshabv-06/openai-chat-app-quickstart)

    to clone the project repository into your local environment.
     Run the command 
    ```
    git clone https://github.com/varshabv-06/openai-chat-app-quickstart
    ```
    to download the project files. After cloning, navigate into the project folder using cd openai-chat-app-quickstart. This process will copy the complete application code to your system, allowing you to work on and deploy the project.

     ![](../media/L2-T2-S1.png)

1. Navigate to the project directory by running the command to access the cloned application files.
     ```
     cd openai-chat-app-quickstart
     ```
1. Configure Environment Variables

    After cloning the repository, run the command
    ```
    ls -a 
    ```
    ![](../media/L2-T2-S3.png)
    to list all files, including hidden ones, in the directory. Look for the file named **.env.sample** in the project folder. Once located, open the file using the command 
    ```
    nano .env.sample
    ```
     Open the file and add the required variables such as
    ```
    AZURE_OPENAI_API_VERSION=2024-02-15-preview
    AZURE_OPENAI_ENDPOINT=
    AZURE_OPENAI_CHAT_DEPLOYMENT=chat-model
    AZURE_TENANT_ID=

    ```

   >**Important:** Use the previously stored endpoint and API key from the Azure AI Foundry portal to configure the **.env file**. Enter the AZURE_OPENAI_ENDPOINT with your saved endpoint URL. Then specify the deployment name as chat-model and set the API version as provided.If a Tenant ID is required, navigate to the **Microsoft Entra ID** in the Azure portal and copy the Tenant ID from there. Paste it into the .env file to complete the setup.
   
   >**Note:** Ensure all values are correctly copied from your saved notes to avoid connection errors. These stored credentials are essential for securely connecting your application to Azure OpenAI. This step completes the configuration required for enabling AI functionality in your app.

   Once editing is completed, press **Ctrl + X** to exit, then press **Y** to confirm saving the changes, and hit **Enter** to save the content.


### Task 3: Create Log Analytics Workspace

In this task, you will create a **Log Analytics Workspace** to monitor and collect logs from your Azure resources. You will navigate to the Azure Portal, provide required details, and deploy the workspace. This will help you track performance and troubleshoot issues in your application.

1. Navigate back to the Azure portal
    ```
    https://portal.azure.com
    ```

1. In the Search bar of the Azure portal, type **Log Analytics workspace (1)**, then select **Log Analytics workspaces (2)**.

    ![](../media/L2-T3-S2.png)

1. Click on + **Create** to create a new workspace.

1. On the Create Log Analytics workspace page, add the below settings and click on **Review + Create (4)**.

      | Setting | Value|
      |----------|--------|
      | Resource Group | |
      | Name | **chatapp-loganalytics<inject key="DeploymentID" enableCopy="false"/> (3)**|
      | Region | **East US (4)**|
    ![](../media/l2-t3-s4.png)
    
1. On the Create Log Analytics workspace page, add the below settings and click on **Review + Create (5)**.

1.  Once the workspace validation has passed, select **Create**. Wait for the new workspace to be provisioned, this may take a few minutes.
