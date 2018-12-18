﻿---
title: Integrate Azure Key Vault in Resource Manager template deployment | Microsoft Docs
description: Learn how to use Azure Key Vault to pass secure parameter values during Resource Manager template deployment
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/27/2018
ms.topic: tutorial
ms.author: jgao
---

# Tutorial: Integrate Azure Key Vault in Resource Manager Template deployment

Learn how to retrieve secure values from Azure Key Vault and pass the secret values as parameters during Resource Manager deployment. The value is never exposed because you only reference its Key Vault ID. For more information, see:

- [Use Azure Key Vault to pass secure parameter value during deployment](./resource-manager-keyvault-parameter.md)
- [Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)

In this tutorial, you create a virtual machine and some dependent resources. It is the same scenario used in [Tutorial: create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md). When creating a virtual machine, you need to provide the administrator username and password. The password is retrieved from an Azure Key Vault.

This tutorial covers the following tasks:

> [!div class="checklist"]
> * Prepare the Key Vault
> * Open a quickstart template
> * Edit the parameters file
> * Deploy the template
> * Validate the deployment
> * Clean up resources

If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/free/) before you begin.

## Prerequisites

To complete this article, you need:

* [Visual Studio Code](https://code.visualstudio.com/) with [Resource Manager Tools extension](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## Prepare the Key Vault

In this section, you use a Resource Manager template to create a Key Vault. This template does:

* Create a Key Vault with the **enabledForTemplateDeployment** property enables. This property must be true before the template deployment process can access the secrets defined in this Key Vault.
* Add a secret to the Key Vault.  The secret is the virtual machine administrator password.

If you (as the user to deploy the virtual machine template) are not the owner of the Key Vault, the Owner or a Contributor of the Key Vault must grant you the access to the Microsoft.KeyVault/vaults/deploy/action permission for the Key Vault.

Your Azure AD user object ID is needed by the template to configure permissions. To retrieve your ID:

1. Sign in to the [Azure Cloud Shell](https://shell.azure.com).
2. Switch to your favorite environment, either **PowerShell** or **Bash** from the upper left corner.
3. Run the following Azure PowerShell or Azure CLI command.  

    ```cli
    az ad user show --upn-or-object-id "<YourEmailAddressAssociatedWithYourSubscription>" --query "objectId"
    ```
    ```powershell
    (Get-AzureADUser -ObjectId "<YourEmailAddressAssociatedWithYourSubscription>").ObjectId
    ```

    The output is a GUID number.

To create a Key Vault:

1. Select the following image to sign in to Azure and open a template. The template creates a Key Vault and a Key Vault secret.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" /></a>

2. Select or enter the following values.  Don't select **Purchase** after you enter the values.

    ![Resource Manager template Key Vault integration deploy portal](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    - **Subscription**: select an Azure subscription.
    - **Resource group**: assign a unique name. Write down this name, you use the same resource group to deploy the virtual machine in the next session. Placing both the Key Vault and the virtual machine in the same resource group makes it easier to clean up the resource at the end of the tutorial.
    - **Location**: select a location.  The default location is **Central US**.
    - **Key Vault Name**: assign a unique name. 
    - **Tenant Id**: the template function automatically retrieve your tenant id.  Don't change the default value
    - **Ad User Id**: enter your Azure AD user object ID that you retrieved from the last procedure.
    - **Secret Name**: The default name is **mAdminPassword**. If you change the secret name here, you need to update the secret name when you deploy the virtual machine.
    - **Secret Value**: Enter your secret.  The secret is the password used to sign in to the virtual machine.
    - **I agree to the terms and conditions state above**: Select.
3. Select **Edit parameters** from the top to take a look of the template.
4. Browse to line 28 of the template JSON file. This is the Key Vault resource definition.
5. Browse to line 35:

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment` is a Key Vault property. This property must be true before you can retrieve the secrets from this Key Vault during deployment. 
6. Browse to line 89. This is the Key Vault secret definition.
7. Select **Discard** from the bottom of the page. You didn't make any changes.
8. Verify you have provided all the values as shown in the previous screenshot, and then click **Purchase** at the bottom of the page.
9. Select the bell icon (notification) from the top of the page to open the **Notifications** pane. Wait until the resource is deployed successfully.
8. Select **Go to resource group** from the **Notifications** pane. 
9. Select the Key Vault name to open it.
10. Select **Access policies** from the left pane. Your name (Active Directory) shall be listed, otherwise you don't have the permission to access the key vault.
11. Select **Click to show advanced access policies**. Notice **Enable access to Azure Resource Manager for template deployment** is selected. This is another condition to make the Key Vault integration to work.

    ![Resource Manager template Key Vault integration access policies](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)    
12. Select **Properties** from the left pane.
13. Make a copy of **Resource ID**. You need this ID when you deploy the virtual machine.  The Resource ID format is:

    ```
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## Open a Quickstart template

Azure QuickStart Templates is a repository for Resource Manager templates. Instead of creating a template from scratch, you can find a sample template and customize it. The template used in this tutorial is called [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. From Visual Studio Code, select **File**>**Open File**.
2. In **File name**, paste the following URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Select **Open** to open the file. It is the same scenario used in [Tutorial: create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
4. Select **File**>**Save As** to save a copy of the file to your local computer with the name **azuredeploy.json**.
5. Repeat step 1-4 to open **https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json**, and then save the file as **azuredeploy.parameters.json**.

## Edit the parameters file

You don't need to make any changes to the template file.

1. Open **azuredeploy.parameters.json** in Visual Studio Code if it is not opened.
2. Update the **adminPassword** parameter to:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    Replace the **id** with the resource ID of your Key Vault created in the last procedure.  

    ![integrate key vault and Resource Manager template virtual machine deployment parameters file](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Give the values to:

    - **adminUsername**: name the virtual machine administrator account.
    - **dnsLabelPrefix**: name the dnsLablePrefix.
4. Save the changes.

## Deploy the template

Follow the instructions in [Deploy the template](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) to deploy the template. You need to upload both **azuredeploy.json** and **azuredeploy.parameters.json** to the Cloud shell, and then use the following PowerShell script to deploy the template:

```powershell
$resourceGroupName = "<Enter the resource group name for the Key Vault>"
$deploymentName = "<Name this deployment>"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

When you deploy the template, use the same resource group as the Key Vault. It makes easier when you clean up the resources. You only need to delete one resource group instead of two.

## Valid the deployment

After you have successfully deployed the virtual machine, test the login using the password stored in the Key Vault.

1. Open the [Azure portal](https://portal.azure.com).
2. Select **Resource grouips**//**YourResourceGroupName>**//**simpleWinVM**
3. Select **connect** from the top.
4. Select **Download RDP File** and then follow the instructions to sign in into the virtual machine.

## Clean up resources

When the Azure resources are no longer needed, clean up the resources you deployed by deleting the resource group.

1. From the Azure portal, select **Resource group** from the left menu.
2. Enter the resource group name in the **Filter by name** field.
3. Select the resource group name.  You shall see a total of six resources in the resource group.
4. Select **Delete resource group** from the top menu.

## Next steps

In this tutorial, you retrieved a secret from Azure Key Vault, and use the secret in your template deployment.  To learn how to create linked templates, see:


> [!div class="nextstepaction"]
> [Create linked templates](./resource-manager-tutorial-create-linked-templates.md)
