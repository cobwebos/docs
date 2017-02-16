---
title: "Azure Functions 的自动化资源部署 | Microsoft Docs"
description: "了解如何生成将 Functions App 部署到 Microsoft Azure 的 Azure Resource Manager 模板。"
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
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>自动部署 Azure Functions App 的资源

在本主题中，你将学习如何生成用于部署 Function App 的 Azure Resource Manager 模板。 以及学习如何定义 Azure Function 所需的资源基线和在执行部署时指定的参数。 根据函数中使用的[触发器和绑定](functions-triggers-bindings.md)，可能需要部署额外的资源以包含整个应用程序作为基础结构即代码。

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)

有关完整模板的示例，请参阅[创建基于消耗的 Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json)和/或[创建基于应用服务计划的 Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)

## <a name="what-is-deployed"></a>部署的内容

使用下面的示例，创建基线 Azure Function App。 Function App 所需资源如下：

* [Azure 存储](../storage/index.md)帐户
* 宿主计划（消耗计划或应用服务计划）
* Function App（**functionapp** 类 Microsoft.Web/站点）

## <a name="parameters"></a>parameters

使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 Parameters 的部分，其中包含所有参数值。 你应为随着要部署的项目或要部署到的环境而变化的值定义参数。

[变量](../azure-resource-manager/resource-group-authoring-templates.md#variables)可用于不根据单个部署而改变的值，或在模板中使用前需要转换的参数（例如，通过验证规则）。

在定义参数时，请使用 **allowedValues** 字段来指定用户在部署过程中可以提供哪些值。 如果在部署过程中未提供任何值，请使用 **defaultValue** 字段为该参数赋值。

描述一下模板的参数。

### <a name="appname"></a>appName

要创建的函数的名称。

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

要部署 Function App 的位置。

> [!NOTE]
> **defaultValue** 参数用于继承资源组的位置，或者如果在 Powershell 或 CLI 部署期间未指定参数值。 如果从门户部署，则会提供一个下拉框，从 **allowedValues** 中选择。

> [!TIP]
> 有关 Azure Functions 可用区域的最新列表，请访问[可用产品(按区域)](https://azure.microsoft.com/regions/services/)页面。

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
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration（可选）

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>变量

除了参数之外，Azure Resource Manager 模板还有关于变量的概念，它可以合并参数以生成模板要使用的更具体的设置。

在下面的示例中，可以看到我们利用变量应用 [Azure Resource Manager 模板函数](../azure-resource-manager/resource-group-template-functions.md)来接受提供的 appName 并将其转换为小写，确保满足 Azure 存储帐户的[命名要求](../storage/storage-create-storage-account.md#create-a-storage-account)。

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>要部署的资源

### <a name="storage-account"></a>存储帐户

Azure 存储帐户是 Azure Functions 中必需的资源。

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

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>宿主计划：消耗计划与应用服务计划

生成函数时，你可能希望函数是完全托管缩放的，即由平台按需缩放（消耗）。 或者，可以选择用户管理的缩放，其中 Functions 会在专用硬件（应用服务计划）上全天候运行，在该硬件中可手动或自动配置实例数。 选用哪种计划可取决于计划的可用功能，或取决于由按成本的构建驱动的决策。 若要了解有关不同宿主计划的详细信息，请参阅[缩放 Azure Functions](functions-scale.md) 一文。

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

#### <a name="app-service-plan"></a>应用服务计划

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

### <a name="function-app-site"></a>Function app（站点）

选择缩放选项后，即可创建用于保存所有函数的容器，它又称为 Function App。

Function App 有许多子资源，你可在其中使用包含“应用设置”和“源代码管理选项”。 可选择删除 **sourcecontrols** 子资源，使用其他[部署选项](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 请务必了解如何在 Azure 中部署资源，以确保使用 Azure Resource Manager 时为应用程序成功创建基础结构即代码配置。 在此示例中，请注意使用 **siteConfig** 应用了顶级配置，在顶级设置这些配置非常重要，因为它们会在应用子资源 **sourcecontrols/web** 之前将意义传达给所需的 Azure Functions 运行时和部署引擎。 虽然可以在子级资源 **config/appSettings** 中配置这些设置，但在某些情况下，需要在应用 **config/appsettings** 之前部署 Function App 和 Functions，因为 Functions 是其他资源（例如[逻辑应用](../logic-apps/index.md)）的依赖项。

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
> 在此模板中使用 [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 应用设置，这是在设置函数部署引擎 (Kudu) 要查找可部署代码的基本目录。 在此示例中，我们将此值设置为 `src`，因为这里的 GitHub 存储库包含一个 `src` 文件夹，其中的函数是其子代。 如果一个存储库中的函数直接位于存储库的根目录中，或者未从源代码管理进行部署，则可以删除此应用设置。

## <a name="deploying-your-template"></a>部署模板

* [Powershell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [门户](../azure-resource-manager/resource-group-template-deploy-portal.md)
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

现在，你已经能够通过代码部署 Function App，请借此机会了解有关如何开发和配置 Azure Functions 的详细信息：

* [Azure Functions 开发人员参考](functions-reference.md)
* [如何配置 Azure Functions 应用设置](functions-how-to-use-azure-function-app-settings.md)
* [创建第一个 Azure 函数](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


