# Lab03: Build, Push and Deploy the Azure OpenAI Chat Application

In this lab, you will containerize and deploy a chat application powered by Azure OpenAI. All required Azure infrastructure has already been provisioned for you — including the Container Registry, Container App, Managed Identity, and Log Analytics Workspace. Your task is to build the application into a Docker image, push it to the registry, configure the connection to Azure OpenAI, and verify the live chat application.


### Estimated Duration: 120 minutes

## Overview

In this lab, you will build the chat application into a Docker image and push it to Azure Container Registry (ACR). After pushing the image, you will configure the application with the required environment variables such as the Azure OpenAI endpoint and deployment details. The application uses a Managed Identity for secure, credential-free access to both ACR and Azure OpenAI — no passwords or API keys are stored anywhere. Finally, you will deploy the configured application and verify it is running end to end.

## Objectives

In this lab, you will perform the following:

- Task 1: Verify Deployed Resources and Gather Required Values
- Task 2: Build, Push and Configure the Chat Application

---

   > **Note:**  Handling Timeout in Cloud Shell
    If a timeout occurs in Cloud Shell during execution, run the following command again to re-authenticate:
    **az login**.
    A prompt will appear with a link (https://login.microsoft.com/device); click the link and open it in your browser. Enter the provided device code and sign in using the given email ID, then click Continue. After successful authentication, return to Cloud Shell and select the required subscription by entering the corresponding number. Once completed, you can proceed with the remaining steps.
    ![](../media/l3-t1-s0.png)

### Task 1: Verify Deployed Resources and Gather Required Values

In this task, you review components like the resource group, container app, registry, and supporting services to ensure everything is properly configured. You also collect important values such as endpoints, keys, and connection details needed for later steps. This task ensures your environment is ready and avoids issues in subsequent deployment or testing stages.

1.  Navigate to the folder:

    ```powershell    
    cd /home/<username>/openai-chat-app-quickstart
    ```

    Get required values from already deployed resources:

1.   Get your OpenAI endpoint:
    Run the following command to list Cognitive Services, and make sure to replace <rg-name> with the provided Resource Group name before executing it:


        ```powershell       
        az cognitiveservices account list `
                --resource-group <rg-name> `
                --query "[].properties.endpoint" -o tsv
        ```

        ![](../media/l3-t1-s2.1.png)

1.   Get your OpenAI account name:
    Run the following command to list OpenAI account name, and make sure to replace <rg-name> with the provided Resource Group name before executing it:

        ```powershell       
        az cognitiveservices account list `
                --resource-group <rg-name> `
                --query "[].{Name:name, Kind:kind}" -o table
        ```

        ![](../media/l3-t1-s2.2.png)

1.    Get your OpenAI deployment name (use the OpenAI account name from above):
    Run the following command to list OpenAI deployment name, and make sure to replace <rg-name> with the provided Resource Group name before executing it:

        ```powershell        
        az cognitiveservices account deployment list `
                --resource-group <rg-name> `
                --name "<your-openai-account-name>" `
                --query "[].name" -o tsv
        ```

        ![](../media/l3-t1-s2.3.png)

1.    Verify deployed resources
    Run this to confirm all resources were created:

        ```powershell    
        az resource list `
            --resource-group <rg-name> `
            --query "[].{Name:name, Type:type}" -o table
        ```

        ![](../media/l3-t1-s3.png)

        > **Note:** After executing the command, note down the generated resource names such as Container Registry, Container Apps Environment, Managed Identity, and Container App. Copy and store these details securely in Notepad for future reference, as they will be required in later steps of the lab.




### Task 2 - Build, Push and Configure the Chat Application

In this task, you will build the Docker image from your source code, push it to ACR, assign the required roles to the Managed Identity, configure the Container App with environment variables, and test the live chat application.

1.    Enable Admin on the Container Registry

        Use the following command to enable admin access for Azure Container Registry. Replace --name with your registry name and --resource-group with your resource group:

        ```powershell    
        az acr update `
            --name <your-registry-name> `
            --resource-group <your-rg-name> `
            --admin-enabled true
        ```

        ![](../media/l3-t2-s1.png)

        > **Note:** This step updates the Azure Container Registry to enable the Admin user. Enabling admin access generates a username and password for the registry. These credentials are used to authenticate Docker for pushing and pulling images. The successful output confirms the registry is updated and ready for use.

1.    Build and Push the image

        This command builds the Docker image and pushes it directly to Azure Container Registry (ACR) without needing a local Docker installation. Replace <your-registry-name> with your registry name and <your-username> with your Cloud Shell username.

        > **Important:** The command must point to the /src subfolder — not the root of the repository. The /src folder contains the Dockerfile and all application code. Using the wrong path will cause the build to fail.


        ```powershell    
                az acr build `
                --registry <your-registry-name> `
                --image openai-chat-app:latest `
                        /home/<your-username>/openai-chat-app-quickstart/src
        ```

   
        ![](../media/l3-t2-s2.png)

         

        Wait for the build to complete. A successful build ends with:
        Run ID: ca1 was successful after Xm Xs

    
        ![](../media/l3-t2-s2.1.png)

1. Verify the image was pushed successfully
    Run the following command to confirm the image exists in your registry.
    Replace <your-registry-name> with your registry name:

    ```powershell    
        az acr repository list `
            --name <your-registry-name> `
            -o tsv
    ```

    ![](../media/l3-t2-s3.png)

    You should see `openai-chat-app` listed in the output.

1.  AcrPull role assignment

    In this step, you assign the AcrPull role to your application's managed identity. This role allows the application (running in Azure Container Apps) to securely pull container images from Azure Container Registry without exposing credentials.

    - **Get the managed identity's principal ID:**


        ```powershell        
        $principalId = az containerapp show --name "your-containerapp-name" --resource-group "your-resource-group" --query "identity.userAssignedIdentities.*.principalId" -o tsv
        ```
        ```powershell
                echo $principalId
        ```

        ![](../media/l3-t2-s3.1.png)


    - **Get the registry resource ID:**

        ```powershell        
        $registryId = az acr show `
                --name <your-registry-name> `
                --resource-group <your-rg-name> `
                --query "id" -o tsv
        ```
        ```powershell
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
        ![](../media/l3-t2-s3.3.png)

        > **Note:** This grants the Container App's managed identity permission to securely pull images from the registry without any passwords.

1.    Get the user assigned identity resource ID and attach registry

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
            --server <your-registry-name>.azurecr.io `
            --identity $identityId
        ```
        
        ![](../media/l3-t2-s5.png)

        > **Note:** This links the Container App to your registry using the Managed Identity for secure access — no passwords are needed.

1.    Update the ingress port


        The application runs on port 50505 as defined in gunicorn.conf.py. The default infrastructure sets port 80 which must be updated before deploying the image, otherwise the Container App will fail to start.

        ```powershell    
        az containerapp ingress update `
            --name <your-containerApp-name> `
            --resource-group <your-rg-name> `
            --target-port 50505
        ```


        ![](../media/l3-t2-s5.1.png)

        > **Note:** If this step is skipped, the revision will show `ActivationFailed` because the Container App will try to reach the app on port 80 while the app is listening on port 50505.

1. Verify the correct Client ID:

    ```powershell
        az identity show `
            --name <your-identity-name> `
            --resource-group <your-rg-name> `
            --query "{ClientId:clientId, PrincipalId:principalId}" -o table
    ```
    Note down the Managed Identity Client ID carefully, as it will be required for authentication and configuration in subsequent steps.


1.    Deploy the image and configure OpenAI connection settings

        Run the following single command to deploy your image, set the required resources, and configure all environment variables at once. Replace the placeholder values with your previously noted OpenAI endpoint, deployment name, and Managed Identity Client ID:

        ```powershell    
        az containerapp update `
            --name <your-containerApp-name> `
            --resource-group <your-rg-name> `
            --image <your-registry-name>.azurecr.io/openai-chat-app:latest `
            --cpu 1.0 `
            --memory 2.0Gi `
            --replace-env-vars `
                AZURE_OPENAI_ENDPOINT="<your-openai-endpoint>" `
                AZURE_OPENAI_CHAT_DEPLOYMENT="<your-openai-deployment-name>" `
                AZURE_OPENAI_API_VERSION="2024-02-15" `
                AZURE_CLIENT_ID="<your-managed-identity-client-id>" `
                RUNNING_IN_PRODUCTION="true"
        ```
        

        ![](../media/l3-t2-s7.1.png)


        > **Note:** All five environment variables are required:
        > - `AZURE_OPENAI_ENDPOINT` — the endpoint URL of your Azure OpenAI resource
        > - `AZURE_OPENAI_CHAT_DEPLOYMENT` — the name of your model deployment (e.g. `chat-model`)
        > - `AZURE_OPENAI_API_VERSION` — the API version to use
        > - `AZURE_CLIENT_ID` — tells `DefaultAzureCredential` which Managed Identity to use for authenticating to Azure OpenAI
        > - `RUNNING_IN_PRODUCTION` — switches the app from local developer credentials to Managed Identity authentication
        >
        > **Important:** Use `--replace-env-vars` (not `--set-env-vars`) to ensure all values are correctly saved to the Container App.

        Verify the revision is Running
    Wait 1–2 minutes then run:

        ```powershell    
        az containerapp revision list `
            --name <your-containerApp-name> `
            --resource-group <your-rg-name> `
            --query "[].{Revision:name, State:properties.runningState, Active:properties.active}" `
            --output table
        ```

        ![](../media/l3-t2-s7.2.png)

        Confirm the latest revision shows **Running** and **Active: True**.

        > **Note:** If the revision shows `ActivationFailed`, verify that Step 6 (ingress port update) was completed before this step, and re-run this command.

1.    Verify the Configuration
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
    - `env` contains all five variables with their values

1.    Get the Application URL
    Run the following command to retrieve the public URL of your Container App:

        ```powershell    
        az containerapp show `
            --name <your-containerApp-name> `
            --resource-group <your-rg-name> `
            --query "properties.configuration.ingress.fqdn" `
            -o tsv
        ```

        Copy the URL from the output.

        ![](../media/l3-t2-s9.png)

1.    Test the Application
    Open your browser and navigate to the URL copied from the previous step:

        https://`<your-container-app-url>`

        ![](../media/l3-t2-s10.png)

        Type a message in the chat box. The application should respond using Azure OpenAI, confirming the full end-to-end connection is working.

> **Congratulations!** You have successfully built, pushed, and deployed the chat application connected to Azure OpenAI.



 **Troubleshooting 1:** If the revision shows ActivationFailed after deploying the image, it means the Container App could not start. The most common causes are:

- Wrong port — ensure Step 6 (ingress port update to 50505) was completed before deploying the image
    AcrPull role not assigned — re-run Step 4 to assign the role and wait 1–2 minutes
    Registry not attached — re-run Step 5 to attach the registry using the Managed Identity

- After fixing, force a new revision by re-running Step 7.


**Troubleshooting 2:** If you see a PermissionDenied error when chatting with the application, the Managed Identity needs the Cognitive Services OpenAI User role on the OpenAI resource. Run the following commands to fix it:

   - Get the user assigned managed identity principal ID:

        ```powershell
        $miPrincipalId = az containerapp show `
        --name <your-container-app-name> `
        --resource-group <your-rg-name> `
        --query "identity.userAssignedIdentities.*.principalId" -o tsv
        ```
   - Get the OpenAI resource ID:

        ```powershell
        $openAiId = az cognitiveservices account show `
        --name <your-openai-account-name> `
        --resource-group <your-rg-name> `
        --query "id" -o tsv
        ```

   - Assign the role:
        ```powershell
        az role assignment create `
        --assignee $miPrincipalId `
        --role "Cognitive Services OpenAI User" `
        --scope $openAiId
        ```


Wait 1–2 minutes for the permission to propagate, then refresh the app in your browser and try again.

**Troubleshooting 3:** If you see ManagedIdentityCredential: No User Assigned or Delegated Managed Identity found for specified ClientId in the chat application, the AZURE_CLIENT_ID value is incorrect.

 -  Verify the correct Client ID:
    ```powershell
    az identity show `
        --name <your-userAssignedIdentities> `
        --resource-group <rg-name> `
        --query "{ClientId:clientId, PrincipalId:principalId}" -o table
    ```
    Copy the ClientId value and re-run Task 2 Step 8 with the correct value. Then wait 2–3 minutes and refresh the browser.

**Troubleshooting 4:** If you see Error code: 404 - Resource not found in the chat application, the deployment name or endpoint is incorrect.


 - Verify what deployments exist:

    ```powershell
        az cognitiveservices account deployment list `
            --resource-group <rg-name> `
            --name openai-azure-chatapp `
            --query "[].{Name:name, Status:properties.provisioningState}" `
            -o table
     ```
    Check what is currently set on the Container App:
    ```powershell
    az containerapp show `
        --name openai-azure-chatapp-app `
        --resource-group <rg-name> `
        --query "properties.template.containers[0].env" `
        -o table
    ```
>**Summarization:**
Build, Push and Deploy the Chat Application (120 min)
Users collect resource values (OpenAI endpoint, deployment name, Managed Identity Client ID), build a Docker image using az acr build from the /src folder, push it to ACR, assign AcrPull and Cognitive Services roles to the Managed Identity, update the ingress port to 50505, deploy the image with all environment variables in one command using --replace-env-vars, and verify the live chat app in the browser.