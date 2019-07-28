---
title: 创建包含自定义操作和资源的 Azure 托管应用程序
description: 本教程介绍如何使用 Azure 自定义提供程序创建 Azure 托管应用程序。
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: 3dd0887114156956b55f554d0265e3ca2b9b10ab
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336102"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>教程：创建包含自定义操作和资源的托管应用程序

在本教程中，你将创建自己的包含自定义操作和资源的托管应用程序。 该托管应用程序在 `Overview` 页上包含一个自定义操作、在 `Table of Content` 中包含一个显示为独立菜单项的自定义资源类型，并在自定义资源页上包含一个自定义上下文操作。

本教程包括以下步骤：

> [!div class="checklist"]
> * 创作用于创建托管应用程序实例的用户界面定义文件
> * 使用 Azure 自定义提供程序、Azure 存储帐户和 Azure 函数创作部署模板
> * 创作包含自定义操作和资源的视图定义项目
> * 部署托管应用程序定义
> * 部署托管应用程序的实例
> * 执行自定义操作并创建自定义资源

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要知道：

* 如何[创建和发布托管应用程序定义](publish-service-catalog-app.md)。
* 如何[通过 Azure 门户部署服务目录应用](deploy-service-catalog-quickstart.md)。
* 如何[为托管应用程序创建 Azure 门户用户界面](create-uidefinition-overview.md)。
* [视图定义项目](concepts-view-definition.md)功能。
* [Azure 自定义提供程序](custom-providers-overview.md)功能。

## <a name="user-interface-definition"></a>用户界面定义

在本教程中，你将创建一个托管应用程序，其托管资源组将包含自定义提供程序实例、存储帐户和函数。 本示例中使用的 Azure 函数实现了一个 API，该 API 可以处理用于执行操作和创建资源的自定义提供程序操作。 Azure 存储帐户用作自定义提供程序资源的基本存储。

用于创建托管应用程序实例的用户界面定义包括 `funcname` 和 `storagename` 输入元素。 存储帐户名称和函数名称必须全局唯一。 默认情况下，将从[示例函数包](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)部署函数文件，但你可以通过以下方式更改此部署方式：为 *createUIDefinition.json* 中的包链接添加一个输入元素：

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

并在 *createUIDefinition.json* 中添加一个输出元素：

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

完整的 *createUIDefinition.json* 示例可在[参考：用户界面元素项目](reference-createuidefinition-artifact.md)中找到。

## <a name="template-with-custom-provider"></a>带有自定义提供程序的模板

若要使用自定义提供程序创建托管应用程序实例，需要在 **mainTemplate.json** 中使用名称 **public** 和类型 **Microsoft.CustomProviders/resourceProviders** 定义自定义提供程序资源。 在该资源中，定义服务的资源类型和操作。 若要部署 Azure 函数和 Azure 存储帐户实例，请分别定义 `Microsoft.Web/sites` 和 `Microsoft.Storage/storageAccounts` 类型的资源。

在本教程中，你将创建一个 `users` 资源类型、`ping` 自定义操作，以及要在 `users` 自定义资源的上下文中执行的 `users/contextAction` 自定义操作。 对于每个资源类型和操作，请提供一个终结点，该终结点指向名称为 [createUIDefinition.json](#user-interface-definition) 中所提供名称的函数。 对于资源类型和操作，请分别指定 `Proxy,Cache` 和 `Proxy` 作为 **routingType**：

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

完整的 *mainTemplate.json* 示例可在[参考：部署模板项目](reference-main-template-artifact.md)中找到。

## <a name="view-definition-artifact"></a>视图定义项目

若要在托管应用程序中定义包含自定义操作和自定义资源的用户界面，需要创作 **viewDefinition.json** 项目。 有关视图定义项目的详细信息，请参阅 [Azure 托管应用程序中的视图定义项目](concepts-view-definition.md)。

在本教程中，你将定义：
* 一个带有工具栏按钮“概述”页，该按钮代表使用基本文本输入的自定义操作 `TestAction`。 
* 一个代表自定义资源类型 `users` 的“用户”页。 
* “用户”页中的一个自定义资源操作 `users/contextAction`，该操作将在类型为 `users` 的自定义资源的上下文中执行。 

以下示例显示了“概述”页的视图配置：

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

以下示例包含具有自定义资源操作的“用户”资源页配置：

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

完整的 *viewDefinition.json* 示例可在[参考：视图定义项目](reference-view-definition-artifact.md)中找到。

## <a name="managed-application-definition"></a>托管应用程序定义

将以下托管应用程序项目打包为 zip 存档，并将其上传到存储：

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

所有文件必须位于根级别。 包含项目的包可以存储在任何存储中，例如 GitHub Blob 或 Azure 存储帐户 Blob。 下面是用于将应用程序包上传到存储帐户的脚本： 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

运行以下 Azure CLI 脚本，或遵循 Azure 门户中的步骤部署服务目录托管应用程序定义：

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 在 Azure 门户中，选择“所有服务”。  在资源列表中，键入并选择“托管应用程序中心”。 
2. 在“托管应用程序中心”上选择“服务目录应用程序定义”，然后单击“添加”。    
    
    ![添加服务目录](./media/managed-application-with-custom-providers/service-catalog-managed-application.png)

3. 提供用于创建服务目录定义的值：

    * 提供唯一的服务目录定义**名称**、**显示名称**和*说明*（可选）。
    * 选择要在其中创建应用程序定义的**订阅**、**资源组**和**位置**。 可以使用 zip 包所用的同一资源组，或创建新的资源组。
    * 对于“包文件 URI”，请提供在上一步骤中创建的 zip 文件的路径。 

    ![提供值](./media/managed-application-with-custom-providers/add-service-catalog-managed-application.png)

4. 到达“授权和锁定级别”部分时，选择“添加授权”。 

    ![添加授权](./media/managed-application-with-custom-providers/add-authorization.png)

5. 选择一个 Azure Active Directory 组来管理资源，然后选择“确定”。 

   ![添加授权组](./media/managed-application-with-custom-providers/add-auth-group.png)

6. 在提供所有值后，选择“创建”。 

   ![创建托管应用程序定义](./media/managed-application-with-custom-providers/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>托管应用程序实例

部署托管应用程序定义时，请运行以下脚本，或遵循 Azure 门户中的步骤，使用自定义提供程序来部署托管应用程序实例：

# <a name="azure-clitabazurecli-interactive"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portaltabazure-portal"></a>[门户](#tab/azure-portal)

1. 在 Azure 门户中，选择“所有服务”。  在资源列表中，键入并选择“托管应用程序中心”。 
2. 在“托管应用程序中心”上选择“服务目录应用程序”，然后单击“添加”。    

    ![添加托管应用程序](./media/managed-application-with-custom-providers/add-managed-application.png)

3. 在“服务目录应用程序”页上的搜索框中，键入服务目录定义显示名称。  选择在上一步骤中创建的定义，然后单击“创建”。 

    ![选择“服务目录”](./media/managed-application-with-custom-providers/select-service-catalog-definition.png)

4. 提供用于从服务目录定义创建托管应用程序实例的值：

    * 选择要在其中创建应用程序实例的**订阅**、**资源组**和**位置**。
    * 提供唯一的 Azure 函数名称和 Azure 存储帐户名称。

    ![应用程序设置](./media/managed-application-with-custom-providers/application-settings.png)

5. 通过验证后，单击“确定”以部署托管应用程序的实例。  
    
    ![部署托管应用程序](./media/managed-application-with-custom-providers/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>自定义操作和资源

部署服务目录应用程序实例后，你会获得两个新的资源组。 第一个资源组 `applicationGroup` 包含托管应用程序的实例，第二个资源组 `managedResourceGroup` 包含托管应用程序（包括**自定义提供程序**）的资源。

![应用程序资源文件](./media/managed-application-with-custom-providers/application-resource-groups.png)

可以转到托管应用程序实例，在“概述”页中执行**自定义操作**、在“用户”页中创建**用户**自定义资源，并针对自定义资源运行**自定义上下文操作**。

* 转到“概述”页并单击“Ping 操作”按钮：

![执行自定义操作](./media/managed-application-with-custom-providers/perform-custom-action.png)

* 转到“用户”页并单击“添加”按钮。 提供用于创建资源的输入，并提交表单：

![创建自定义资源](./media/managed-application-with-custom-providers/create-custom-resource.png)

* 转到“用户”页，选择一个“用户”资源，然后单击“自定义上下文操作”：

![创建自定义资源](./media/managed-application-with-custom-providers/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>寻求帮助

如果你遇到了 Azure 托管应用程序方面的问题，请尝试在 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps) 上提问。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 `azure-managedapps` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

若要将托管应用程序发布到 Azure 市场，请参阅[市场中的 Azure 托管应用程序](publish-marketplace-app.md)。
