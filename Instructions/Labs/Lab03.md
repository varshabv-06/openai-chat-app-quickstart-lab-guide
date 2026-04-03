# Lab03: Deploy Azure Resources using Bicep

We deploy Azure resources using Bicep to automate the creation and configuration of infrastructure instead of setting up everything manually. It ensures consistency, reduces human errors, and saves time by deploying all required resources. Using Bicep also makes the setup reusable and easy to manage, as the same template can be used multiple times across environments.

### Estimated Duration:

## Overview

In this task, you will use a Bicep file to deploy all required Azure resources in an automated and efficient manner. Instead of manually creating each service, the Bicep template provisions everything in a single step. This ensures consistency, reduces errors, and speeds up the deployment process. By the end of this task, your complete infrastructure will be ready to host and run the application.

## Objectives

In this lab, you will perform the following:

- Task 1: Deploy Azure Resources using Bicep
- Task 2: Build, Push and Configure the Chat Application

### Task 1 - Deploy Azure Resources using Bicep

In this task, you will use a Bicep file to automatically deploy all required Azure resources for the application. Instead of creating each service manually, the Bicep template defines the infrastructure as code and provisions resources like Container Apps, Container Registry, and Log Analytics in a single command. This approach ensures consistency, reduces manual errors, and speeds up the deployment process.

   > **Note:**  Handling Timeout in Cloud Shell
    If a timeout occurs in Cloud Shell during execution, run the following command again to re-authenticate:
    **az login**.
    A prompt will appear with a link (https://login.microsoft.com/device); click the link and open it in your browser. Enter the provided device code and sign in using the given email ID, then click Continue. After successful authentication, return to Cloud Shell and select the required subscription by entering the corresponding number. Once completed, you can proceed with the remaining steps.

1. Clone the repository

    ```
    git clone https://github.com/varshabv-06/openai-chat-app-quickstart.git
    ```
    ```
    cd openai-chat-app-quickstart
    ```
2. Get required values from already deployed resources

   - **Get your Log Analytics Workspace name:**

        Run the following command to list Log Analytics Workspaces, and make sure to replace <rg name> with the provided Resource Group name before executing it:
        ```
        az monitor log-analytics workspace list `
        --resource-group <rg-name>`
        --query "[].name" -o tsv
        ```
        ![](../media/l3-t1-s2.png)

   - **Get your OpenAI endpoint:**

      Run the following command to list Cognitive Services, and make sure to replace <rg name> with the provided Resource Group name before executing it:

        ```
        az cognitiveservices account list `
        --resource-group <rg-name>  `
        --query "[].properties.endpoint" -o tsv
        ```
        ![](../media/l3-t1-s2.1.png)

   - **Get your OpenAI account name:**

        Run the following command to list OpenAI account name, and make sure to replace <rg name> with the provided Resource Group name before executing it:

        ```
        az cognitiveservices account list `
        --resource-group <rg-name> `
        --query "[].{Name:name, Kind:kind}" -o table
        ```
        ![](../media/l3-t1-s2.3.png)

    - **Get your OpenAI deployment name** (use the OpenAI account name from above):

        Run the following command to list OpenAI deployment name, and make sure to replace <rg name> with the provided Resource Group name before executing it:

        ```
        az cognitiveservices account deployment list `
        --resource-group <rg-name> `
        --name "openai-azure-chat-app" `
        --query "[].name" -o tsv
        ```
        ![](../media/l3-t1-s2.2.png)

    > **Important:** Note all four values — you will need them in the next step.

1. Deploy the infrastructure

    Run the following command, replacing the placeholder values with what
you collected in Step 2:

    ```powershell
    az deployment group create `
    --resource-group <rg-name> `
    --template-file /home//openai-chat-app-quickstart/infra/main.bicep `
    --parameters `
        name="" `
        location="eastus" `
        openAiResourceLocation="eastus" `
        openAiDeploymentName="" `
        openAiModelName="gpt-4o-mini" `
        openAiModelVersion="2024-07-18" `
        openAiDeploymentCapacity=10 `
        openAiDeploymentSkuName="Standard" `
        createAzureOpenAi=false `
        openAiEndpoint="" `
        openAiApiVersion="2024-02-01"
    ```
    ![](../media/l3-t1-s3.png)

    Wait for the deployment to complete. You will see `"provisioningState": "Succeeded"` in the output.

4. Verify deployed resources

    Run this to confirm all resources were created:
    ```
    az resource list `
    --resource-group <rg-name> `
    --query "[].{Name:name, Type:type}" -o table
    ```
    ![](../media/l3-t1-s4.png)

### Task 2 - Build, Push and Configure the Chat Application

In this task, you will build the chat application into a Docker image and push it to a container registry for storage. After pushing the image, you will configure the application with required environment variables such as Azure OpenAI endpoint, API key, and deployment details. This ensures the application can securely connect to Azure services. Finally, the configured application is deployed and made ready to run in a cloud environment.






