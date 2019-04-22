---
title: 为 Azure 逻辑应用创建部署模板 | Microsoft Docs
description: 使用 Azure 资源管理器模板部署逻辑应用
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894228"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>使用 Azure 资源管理器模板部署逻辑应用

创建逻辑应用时，可将逻辑应用的定义扩展到 [Azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)。 然后，可以使用此模板通过以下方式自动完成部署：定义要用于部署的资源和参数，并通过[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)提供参数值。
这样可以更轻松地将逻辑应用部署到所需的任何环境或 Azure 资源组。 

Azure 逻辑应用提供一个可以重复使用的[预生成逻辑应用 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，使用它不仅可以创建逻辑应用，还可以定义要对部署使用的资源和参数。 可以将此模板用于自己的业务方案，也可以根据需要自定义此模板。 详细了解[资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)。 有关 JSON 语法和属性，请参阅 [Microsoft.Logic 资源类型](/azure/templates/microsoft.logic/allversions)。

有关 Azure 资源管理器模板的详细信息，请参阅以下文章：

* [创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)
* [开发用于实现云一致性的 Azure 资源管理器模板](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>逻辑应用的结构

逻辑应用定义包含以下基本节，从“设计器视图”切换为“代码视图”，或者使用 [Azure 资源浏览器](http://resources.azure.com)等工具，即可查看这些节。 逻辑应用定义使用 Javascript 对象表示法 (JSON)。有关 JSON 语法和属性的详细信息，请参阅 [Microsoft.Logic 资源类型](/azure/templates/microsoft.logic/allversions)。

* **逻辑应用资源**：描述逻辑应用的位置或区域、定价计划和工作流定义等信息。

* **工作流定义**：描述逻辑应用的触发器和操作，以及 Azure 逻辑应用服务如何运行工作流。 在代码视图中，可以在 `definition` 节中找到工作流定义。

* **连接**：如果在逻辑应用中使用托管连接器，则 `$connections` 节会引用其他资源，这些资源用于安全存储有关该逻辑应用与其他系统或服务之间的这些连接的元数据，例如连接字符串和访问令牌。 对这些连接的引用显示在逻辑应用定义的 `parameters` 节中。

若要创建可用于 Azure 资源组部署的逻辑应用模板，必须定义资源并根据需要将其参数化。 例如，如果部署到开发、测试和生产环境，则可能想要在每个环境中使用不同的连接字符串连接到 SQL 数据库。
或者，可能想要在不同的订阅或资源组中进行部署。

## <a name="create-logic-app-deployment-templates"></a>创建逻辑应用部署模板

若要以最简单的方式创建有效的逻辑应用部署模板，请使用 Visual Studio 以及“适用于 Visual Studio 的 Azure 逻辑应用工具”扩展。 将逻辑应用从 Azure 门户下载到 Visual Studio 后，将会获得一个可在任何 Azure 订阅和位置中使用的有效部署模板。 此外，下载逻辑应用会自动参数化模板中嵌入的逻辑应用定义。
有关在 Visual Studio 中创建和管理逻辑应用的详细信息，请参阅[使用 Visual Studio 创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)和[使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。

如果不使用 Visual Studio 或者不想根据本主题中的指导手动创建模板和所需的参数，则也可以[使用 PowerShell 模块来创建逻辑应用模板](https://github.com/jeffhollan/LogicAppTemplateCreator)。 此开源模块首先评估逻辑应用，以及逻辑应用使用的任何连接。 然后，该模块生成模板资源以及用于部署的必需参数。 例如，假设某个逻辑应用要从 Azure 服务总线队列接收消息，并将数据添加到 Azure SQL 数据库。 模块工具会保留所有业务流程逻辑，并参数化 SQL 和服务总线连接字符串，使你能够在部署时设置这些值。

> [!IMPORTANT]
> 连接必须位于逻辑应用所在的同一个 Azure 资源组中。
> 要使 PowerShell 模块能够与任何 Azure 租户和订阅访问令牌配合工作，请结合 [Azure 资源管理器客户端工具](https://github.com/projectkudu/ARMClient)使用该模块。 有关详细信息，请参阅这篇[有关 Azure 资源管理器客户端工具的文章](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)，其中更详细地介绍了 ARMClient。

### <a name="install-powershell-module-for-logic-app-templates"></a>安装用于逻辑应用模板的 PowerShell 模块

使用以下命令可以最方便地从 [PowerShell 库](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)安装该模块：

`Install-Module -Name LogicAppTemplate`

也可以手动安装 PowerShell 模块：

1. 下载最新的[逻辑应用模板创建程序](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。

1. 提取 PowerShell 模块文件夹中的文件夹，通常是 `%UserProfile%\Documents\WindowsPowerShell\Modules`。

### <a name="generate-logic-app-template---powershell"></a>生成逻辑应用模板 - PowerShell

安装 PowerShell 之后，可以使用以下命令来生成模板：

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 是 Azure 订阅 ID。 此命令行首先通过 ARMClient 获取访问令牌，然后将其传输到 PowerShell 脚本，再在 JSON 文件中创建模板。

## <a name="parameters-in-logic-app-templates"></a>逻辑应用模板中的参数

创建逻辑应用模板后，可以添加和编辑任何所需参数。 模板包含多个 `parameters` 节，例如： 

* 逻辑应用的工作流定义包含其自身的 [`parameters` 节](../logic-apps/logic-apps-workflow-definition-language.md#parameters)，在其中可以定义逻辑应用在部署时用来接受输入的所有参数。

* 资源管理器模板具有其自身的 [`parameters` 节](../azure-resource-manager/resource-group-authoring-templates.md#parameters)，该节不同于逻辑应用的 `parameters` 节。 例如：

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

例如，假设逻辑应用定义引用了一个表示 Azure 函数或嵌套逻辑应用工作流的资源 ID，而你想要将该资源 ID 连同逻辑应用一起作为单个部署进行部署。 在这种情况下，可将该 ID 作为资源添加到模板中，并参数化该 ID。 针对你要部署到每个 Azure 资源组中的自定义 API 或 OpenAPI 终结点（以前称为“Swagger”）的引用，可以使用上述相同的方法。

在部署模板中使用参数时，请遵循以下指导，使逻辑应用设计器能够正确显示这些参数：

* 仅在以下触发器和操作中使用参数：

  * Azure Functions 应用
  * 嵌入的逻辑应用工作流或子逻辑应用工作流
  * API 管理调用
  * API 连接运行时 URL
  * API 连接路径

* 定义参数时，请确保使用 `defaultValue` 属性值提供默认值，例如：

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* 为了在模板中保护或隐藏敏感信息，请保护参数。 详细了解[如何使用受保护的参数](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)。

以下示例演示如何参数化 Azure 服务总线“发送消息”操作：

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>引用依赖资源

如果逻辑应用要求引用依赖资源，你可以在逻辑应用的部署模板中使用 [Azure 资源管理器模板函数](../azure-resource-manager/resource-group-template-functions.md)。 例如，假设你希望逻辑应用使用合作伙伴、协议，以及要连同逻辑应用一起部署的其他项目的定义来引用某个 Azure 函数或集成帐户。
在这种情况下，可以使用资源管理器模板函数，例如 `parameters`、`variables`、`resourceId`、`concat`，等等。

以下示例演示如何通过定义这些参数来替换 Azure 函数的资源 ID：

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

下面演示了在引用 Azure 函数时如何使用这些参数：

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>将逻辑应用添加到资源组项目

如果你有现有的 Azure 资源组项目，可以使用“JSON 大纲”窗口将逻辑应用添加到该项目。 还可以添加另一个逻辑应用和之前创建的应用。

1. 在“解决方案资源管理器”中，打开 `<template>.json` 文件。

2. 在“视图”菜单中，选择“其他窗口” > “JSON 大纲”。

3. 若要将资源添加到模板文件，请在“JSON 大纲”窗口顶部选择“添加资源”。 或者在“JSON 大纲”窗口中，右键单击“资源”，并选择“添加新资源”。

   ![“JSON 大纲”窗口](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. 在“添加资源”对话框中，找到并选择“逻辑应用”。 为逻辑应用命名，并选择“添加”。

   ![添加资源](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>获取支持

有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署逻辑应用模板](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
