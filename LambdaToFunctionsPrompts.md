# **Lambda to Functions Migration: AI Prompt Library**

## **Base Prompt**

**Prompt Name:** *Lambda to Azure Functions Migration – Full Stack*

You are a **Cloud Solution Architect** who can transform AWS serverless applications to Azure, including code and infrastructure conversion.

> 🔗 **Source AWS Application**: [AWS Lambda + API Gateway Example](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/cross-services/lambda-api-gateway)

### **Tasks**
Please help with the following:

1. Generate a **comparison table** of AWS services/configuration vs Azure equivalents.  
2. Provide the **equivalent project structure** in Azure.  
3. Transform `mylambdafunction.js` and related **YAML** infra files to Azure code and **`azd`-compatible Bicep** files.  
4. Create a **README** file with instructions to:
   - Test the app locally  
   - Deploy to Azure using `azd`

> **Before starting, read all the reference links and think through the solution before generating output.**

---

### **Instructions & Guidelines**

1. Learn how to construct **Bicep files** using best practices like **managed identity** and **VNet integration** from the [official migration guide](https://learn.microsoft.com/en-us/azure/azure-functions/migration/migrate-lambda-workloads).
2. Reference Bicep patterns—especially for **Flex Consumption Plan configuration**—from this [Azure OpenAI AI Search Node sample](https://github.com/Azure-Samples/azure-functions-openai-aisearch-node).
3. Use:
   - **Node.js v4 programming model**
   - **Python v2 programming model**
4. Runtime versions:
   - Node.js: `v20`
   - Python: `3.11`  
   → Refer to [Supported Languages](https://learn.microsoft.com/en-us/azure/azure-functions/supported-languages)
5. Use **Flex Consumption Plan (FC1)** for Azure Functions.
6. Follow **infra best practices** from this [Quickstart Infra Repo](https://github.com/Azure-Samples/functions-quickstart-javascript-azd/tree/main/infra), including app settings and managed identity.
7. Enable **monitoring** setup in your solution.
8. Use **Function Bindings** to connect to Azure services—**not** the Azure SDK directly.
9. Ensure `local.settings.json` is written correctly.
10. File structure must be **`azd` compatible**, including:
    - `azure.yaml`
    - `main.parameters.json`
11. Add **logging statements** in function code.
12. Avoid using app settings meant for other SKUs (e.g., `function worker runtime`, `website content share`, etc.)  
    Only include:
    - Managed identity settings
    - Blob/Queue/Table URIs
13. Keep the **migrated structure in a new folder**, not in the same source directory.
14. In `azure.yaml`, use `language: js` (not `node`) for JS projects.
15. In `local.settings.json`, add:
    ```json
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>"
    ```
16. **Before deviating from these instructions based on new findings, confirm with me first.**

---

## **Specialized Prompts by Pattern**

---

### **1. S3 – Lambda – S3 (Blob Trigger)**

**Blob Trigger (Event Grid based) based Function App** :

1. Follow this [tutorial](https://learn.microsoft.com/en-us/azure/azure-functions/functions-event-grid-blob-trigger?pivots=programming-language-python) to configure blob trigger using Event Grid.
2. Provision the Function App and deploy the code **before** creating the event subscription.
3. Use the **blob_extension** system key to create a **webhook-based event subscription**.
4. Required **roles** for user-assigned identity:
   - Storage Blob Data Owner
   - Storage Blob Queue Data Contributor
   - Storage Blob Queue Data Reader
5. Format of webhook endpoint:
   ```
   https://<FUNCTION_APP_NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.EventGridBlobTrigger&code=<BLOB_EXTENSION_KEY>
   ```
6. You may reuse the same storage account for `AzureWebJobsStorage`, but create a separate app setting for blob trigger connection.

---

### **2. API Gateway – Lambda – DynamoDB (Cosmos DB)**

**Cosmos DB + HTTP triggered Function App**

- If required for local testing, **create Cosmos DB database/container using `azd`** (rely on existing Bicep infra).
- If using **Azure Communication Services**, help register the resource provider if not already done.
- Ensure **Function App uses BlobContainer** storage, not AzureFiles.
- Assist with **local testing setup** before deploying to Azure.
- If **Cosmos DB Emulator** fails, use the real Cosmos DB for local testing and update:
   - Connection details in `function code`
   - `local.settings.json`
   - Any relevant test data files
- Prefer **input/output bindings** over direct Azure SDK usage.
- Create the `function-deployments` container in storage to prevent `azd deploy` errors.
- Use this **Cosmos DB Data Reader role ID** in Bicep:
   ```
   fbdf93bf-df7d-467e-a4d2-9458aa1360c8
   ```
