---
title: "自动化 Azure Functions 应用的资源部署 | Microsoft Docs"
description: "了解如何生成部署 Azure Functions 应用的 Azure Resource Manager 模板。"
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions 无服务体系结构, 基础结构即代码, azure resource manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Azure Functions 应用的自动化资源部署

可以使用 Azure Resource Manager 模板来部署 Azure Functions 应用。 了解如何定义 Azure Functions 应用所需的基线资源和部署时指定的参数。 要成功完成应用程序的“基础结构即代码”配置，可能需要部署其他资源，具体取决于 Function 应用中的[触发器和绑定](functions-triggers-bindings.md)。

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)。

有关完整模板的示例，请参阅[创建基于消耗计划的 Azure Functions 应用](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json)和[创建基于应用服务计划的 Azure Functions 应用](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)。

## <a name="required-resources"></a>所需资源

可以使用本文中的示例创建基线 Azure Functions 应用。 应用需要以下资源：

* [Azure 存储](../storage/index.md)帐户
* 托管计划（消耗计划或 Azure 应用服务计划）
* Function 应用（`type`：**Microsoft.Web/站点**，`kind`：**functionapp**）

## <a name="parameters"></a>parameters

可以使用 Azure Resource Manager 定义部署模板后想要指定的值的参数。 模板的“参数”部分包含所有参数值。 为随着要部署的项目或要部署到的环境而变化的值定义参数。

[变量](../azure-resource-manager/resource-group-authoring-templates.md#variables)可用于不根据单个部署而改变的值，以及在模板中使用前需要转换的参数（例如，通过验证规则）。

定义参数时，请使用 **allowedValues** 字段指定用户在部署过程中可以提供哪些值。 如果在部署过程中未提供任何值，请使用 **defaultValue** 字段为该参数赋值。

Azure Resource Manager 模板使用以下参数。

### <a name="appname"></a>appName

想要创建的 Azure Functions 应用的名称。

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

要将 Function 应用部署到的位置。

> [!NOTE]
> 若要继承资源组的位置，或者如果在 PowerShell 或 Azure CLI 部署过程中未指定参数值，请使用 **defaultValue** 参数。 如果从 Azure 门户部署应用，请在 **allowedValues** 参数下拉列表框中选择一个值。

> [!TIP]
> 有关可使用 Azure Functions 的区域的最新列表，请访问[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL（可选）

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch（可选）

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration（可选）

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>变量

Azure Resource Manager 模板使用变量来引入参数，因此可以在模板中使用更具体的设置。

在下一个示例中，为满足 Azure 存储帐户[命名要求](../storage/storage-create-storage-account.md#create-a-storage-account)，我们使用变量来应用 [Azure Resource Manager 模板函数](../azure-resource-manager/resource-group-template-functions.md)，从而将输入的 **appName** 值转换为小写形式。

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>要部署的资源

### <a name="storage-account"></a>存储帐户

Azure Functions 应用需要 Azure 存储帐户。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>托管计划：消耗计划与App Service

在某些情况下，可能希望函数根据平台按需缩放，这也称为完全托管缩放（通过使用消耗托管计划）。 或者，可以为函数选择用户管理的缩放。 在用户管理的缩放情况下，函数在专用硬件上全天候运行（通过使用应用服务托管计划）。 可手动或自动设置实例数。 可根据计划中的可用功能或构建成本选择托管计划。 若要了解有关托管计划的详细信息，请参阅[缩放 Azure Functions](functions-scale.md)。

#### <a name="consumption-plan"></a>消耗量计划

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>App Service 计划

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="functions-app-a-site"></a>Function 应用（站点）

选择缩放选项后，创建 Function 应用。 该应用是一个包含所有函数的容器。

Function 应用有许多可用于部署的子资源，包括应用设置和源代码管理选项。 还可以选择删除 **sourcecontrols** 子资源，改用另一个[部署选项](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 若要使用 Azure Resource Manager 为应用程序成功创建“基础结构即代码”配置，了解如何在 Azure 中部署资源尤为重要。 在下面的示例中，通过使用 **siteConfig** 应用顶级配置。 请务必在顶级设置这些配置，因为这些配置会将信息传达给 Azure Functions 运行时并部署引擎。 应用 **sourcecontrols/web** 子资源前，需要顶级信息。 虽然可以在子级别 **config/appSettings** 资源中配置这些设置，但在某些情况下，需要在应用 **config/appSettings** 之前部署 Function 应用和函数。 在某些情况下，比如在[逻辑应用](../logic-apps/index.md)中，函数是另一资源的依赖项。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 此模板使用 [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 应用设置值，这将设置基本目录，函数部署引擎 (Kudu) 在此目录中查找可部署代码。 在存储库内，函数位于 **src** 文件夹的子文件夹中。 因此，在前一个示例中，将应用设置值设置为 `src`。 如果函数位于存储库的根目录中，或者不从源代码管理进行部署，则可删除此应用设置值。

## <a name="deploy-your-template"></a>部署模板

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>“部署到 Azure”按钮

将 ```<url-encoded-path-to-azuredeploy-json>``` 替换为 GitHub 中 `azuredeploy.json` 文件的原始路径的 [URL 编码](https://www.bing.com/search?q=url+encode)版本。

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>后续步骤

深入了解如何开发和配置 Azure Functions。

* [Azure Functions 开发人员参考](functions-reference.md)
* [如何配置 Azure Functions 应用设置](functions-how-to-use-azure-function-app-settings.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


