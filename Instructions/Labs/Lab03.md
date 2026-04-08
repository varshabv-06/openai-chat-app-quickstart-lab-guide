# Lab03: Deploy Azure Resources using Bicep

We deploy Azure resources using Bicep to automate the creation and configuration of infrastructure instead of setting up everything manually. It ensures consistency, reduces human errors, and saves time by deploying all required resources. Using Bicep also makes the setup reusable and easy to manage, as the same template can be used multiple times across environments.

### Estimated Duration: 120 minutes

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
    ![](../media/l3-t1-s0.png)

1. Navigate to the folder

    ```
    cd /home/<username>/openai-chat-app-quickstart
    ```
1. Get required values from already deployed resources

   - **Get your OpenAI endpoint:**

      Run the following command to list Cognitive Services, and make sure to replace <rg name> with the provided Resource Group name before executing it:

        ```powershell
        az cognitiveservices account list ` --resource-group <rg-name> ` --query "[].properties.endpoint" -o tsv
        ```
        ![](../media/l3-t1-s2.1.png)

   - **Get your OpenAI account name:**

        Run the followi
        ng command to list OpenAI account name, and make sure to replace <rg name> with the provided Resource Group name before executing it:

        ```powershell
        az cognitiveservices account list `
        --resource-group <rg-name> `
        --query "[].{Name:name, Kind:kind}" -o table
        ```
        ![](../media/l3-t1-s2.3.png)

    - **Get your OpenAI deployment name** (use the OpenAI account name from above):

        Run the following command to list OpenAI deployment name, and make sure to replace <rg name> with the provided Resource Group name before executing it:

        ```powershell
        az cognitiveservices account deployment list `
        --resource-group <rg-name> `
        --name "openai-azure-chat-app" `
        --query "[].name" -o tsv
        ```
        ![](../media/l3-t1-s2.2.png)

    > **Important:** Note all values — you will need them in the next step.

1. Deploy the infrastructure

    Run the following command, replacing the placeholder values with what you collected in Step 2:

    ```powershell
    az deployment group create `
    --resource-group <rg-name> `
    --template-file /home/<your-username>/openai-chat-app-quickstart/infra/main.bicep `
    --parameters `
        name="<your-unique-name>" `
        location="eastus" `
        openAiResourceLocation="eastus" `
        openAiDeploymentName="<your-deployment-name>" `
        openAiModelName="gpt-4o-mini" `
        openAiModelVersion="2024-07-18" `
        openAiDeploymentCapacity=10 `
        openAiDeploymentSkuName="Standard" `
        createAzureOpenAi=false `
        openAiEndpoint="<your-openai-endpoint>" `
        openAiApiVersion="2024-02-01"
    ```
    ![](../media/l3-t1-s3.png)

    ![](../media/l3-t1-s3.1.png)
    > **Note:** For `name`, use a short unique value such as your username
    with no spaces or special characters. For example: `john01`.
    For `--template-file`, replace the path with your actual Cloud Shell
    username. For `openAiDeploymentName` and `openAiEndpoint`, use the
    values collected in Step 2.

    Wait for the deployment to complete. You will see `"provisioningState": "Succeeded"` in the output.

1. Verify deployed resources

    Run this to confirm all resources were created:
    ```powershell
    az resource list `
    --resource-group <rg-name> `
    --query "[].{Name:name, Type:type}" -o table
    ```
    ![](../media/l3-t1-s4.png)

    > **Note:** After executing the command, note down the generated resource names such as Container Registry, Container Apps Environment, Managed Identity, and Container App. Copy and store these details securely in Notepad for future reference, as they will be required in later steps of the lab.

### Task 2 - Build, Push and Configure the Chat Application

In this task, you will build the chat application into a Docker image and push it to a container registry for storage. After pushing the image, you will configure the application with required environment variables such as Azure OpenAI endpoint, API key, and deployment details. This ensures the application can securely connect to Azure services. Finally, the configured application is deployed and made ready to run in a cloud environment.

1. Enable Admin on the Container Registry

    Use the following command to enable admin access for Azure Container Registry, and replace the --name value with the previously stored registry name and --resource-group with your resource group:

    ```powershell
    az acr update `
    --name <your-registry-name> `
    --resource-group <your-rg-name> `
    --admin-enabled true
    ```
    ![](../media/l3-t2-s1.png)

    This step updates the Azure Container Registry to enable the Admin user.
    Enabling admin access generates a username and password for the registry.
    These credentials are used to authenticate Docker for pushing and pulling images.
    The successful output confirms the registry is updated and ready for use.

1. Build and Push the image

    This command is used to build the Docker image and push it directly to Azure Container Registry (ACR) without needing a local Docker build. Replace the --registry value with your previously stored registry name before running the command.

    ```powershell
    az acr build `
    --registry <your-registry-name> `
    --image openai-chat-app:latest `
    /home/<your-username>/openai-chat-app-quickstart/src
    ```
    ![](../media/l3-t2-s2.png)

1. Verify the image was pushed successfully

    Run the following command to confirm the image exists in your registry.
    Replace `<your-registry-name>` with your registry name:

    ```powershell
        az acr repository list `
        --name <your-registry-name> `
        -o tsv
    ```
    ![](../media/l3-t2-s3.png)

1. AcrPull role assignment

    In this step, you assign the AcrPull role to your application’s managed identity. This role allows the application (running in Azure Container Apps) to securely pull container images from Azure Container Registry. By granting this permission, you ensure that the application can access and deploy the required Docker images without exposing credentials.

    - **Get the managed identity's principal ID:**
        ```powershell
        $principalId = az containerapp show `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --query "identity.userAssignedIdentities.*.principalId" -o tsv

        echo $principalId
        ```
        ![](../media/l3-t2-s3.1.png)

    -  **Get the registry resource ID:**
        ```powershell
            $registryId = az acr show `
            --name <your-registry-name> `
            --resource-group <your-rg-name> `
            --query "id" -o tsv

            echo $registryId
        ```
        ![](../media/l3-t2-s3.2.png)

    - **Assign the AcrPull role:**
        ```powershell
            az role assignment create `
            --assignee $principalId `
            --role AcrPull `
            --scope $registryId
        ```
        ![](../media/l3-t2-s3.2.1.png)

     > **Note:** This grants the Container App's managed identity
    permission to securely pull images from the registry without
    any passwords.


1. Get the user assigned identity resource ID and attach registry:
    ```powershell
        $identityId = az containerapp show `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --query "identity.userAssignedIdentities | keys(@) | [0]" -o tsv
    ```

    ```powershell
        az containerapp registry set `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --server .azurecr.io `
        --identity $identityId
    ```
    ![](../media/l3-t2-s4.png)

   > **Note:** This links the Container App to your registry using the
    Managed Identity for secure access — no passwords are needed.

1. Update the Container App with your new image

    Run the following command to replace the default placeholder image
    with your newly built application image:

    ```powershell
        az containerapp update `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --image <your-registry-name>.azurecr.io/openai-chat-app:latest
    ```
    ![](../media/l3-t2-s3.3.png)

    ![](../media/l3-t2-s3.4.png)

1. Configure OpenAI Connection Settings

    Run the following command to set the environment variables that
    connect your app to Azure OpenAI. Replace the values with your
    previously noted OpenAI endpoint and deployment name:

    ```powershell
        az containerapp update `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --set-env-vars `
            AZURE_OPENAI_ENDPOINT="<your-openai-endpoint>" `
            AZURE_OPENAI_CHAT_DEPLOYMENT="<your-openai-chat-deployement-name>" `
            AZURE_OPENAI_API_VERSION="2024-02-01"
    ```
    ![](../media/l3-t2-s7.png)

    ![](../media/l3-t2-s7.1.png)
    This is your live application URL

    ![](../media/l3-t2-s7.2.png)
    Shows your app is deployed using the image from ACR.

    > **Note:** These environment variables tell the application which
    Azure OpenAI resource and model deployment to connect to at runtime.

1. Verify the Configuration

    Run the following command to confirm the image and environment variables are correctly applied:

    ```powershell
        az containerapp show `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --query "{Image:properties.template.containers[0].image, Env:properties.template.containers[0].env}" `
        -o json
    ```
    ![](../media/l3-t2-s8.png)

    Confirm the following in the output:
    - `image` shows your registry URL ending in `openai-chat-app:latest`
    - `env` contains `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_CHAT_DEPLOYMENT`
      and `AZURE_OPENAI_API_VERSION` with your values

1. Get the Application URL

    Run the following command to retrieve the public URL of your
    Container App:
    ```powershell
        az containerapp show `
        --name <your-containerApp-name> `
        --resource-group <your-rg-name> `
        --query "properties.configuration.ingress.fqdn" `
        -o tsv
    ```
    Copy the URL from the output.

    ![](../media/l3-t2-s9.png)

1. Test the Application

    Open your browser and navigate to the URL copied from the
    previous step:

    ```
    https://<your-container-app-url>
    ```
    ![](../media/l3-t2-s10.png)

    Type a message in the chat box. The application should respond
    using Azure OpenAI confirming the full end-to-end connection
    is working.

> **Congratulations!** You have successfully built, pushed, and
    deployed the chat application connected to Azure OpenAI.

> **Troubleshooting:** If you see a `PermissionDenied` error when
chatting with the application, the managed identity needs the
**Cognitive Services OpenAI User** role on the OpenAI resource.
Run the following commands to fix it:

**Get the user assigned managed identity principal ID:**
```powershell
$miPrincipalId = az containerapp show `
  --name <your-container-app-name> `
  --resource-group <your-rg-name> `
  --query "identity.userAssignedIdentities.*.principalId" -o tsv
```

**Get the OpenAI resource ID:**
```powershell
$openAiId = az cognitiveservices account show `
  --name <your-openai-account-name> `
  --resource-group <your-rg-name> `
  --query "id" -o tsv
```

**Assign the role:**
```powershell
az role assignment create `
  --assignee $miPrincipalId `
  --role "Cognitive Services OpenAI User" `
  --scope $openAiId
```

Wait 1-2 minutes for the permission to propagate, then refresh
the app in your browser and try again.





