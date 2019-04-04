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
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894228"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>使用 Azure 资源管理器模板部署逻辑应用

创建逻辑应用时，可以扩展到逻辑应用定义[Azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)。 然后可以使用此模板，用于通过定义资源自动部署和要用于部署和提供通过参数值的参数[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)。
这样一来，你可以部署逻辑应用更轻松地并且想对任何环境或 Azure 资源组。 

Azure 逻辑应用提供[预先构建的逻辑应用 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，可以重复使用，不只用于创建逻辑应用，而且还可以定义的资源和要用于部署参数。 可以将此模板用于自己的业务方案，也可以根据需要自定义此模板。 详细了解如何[资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)。 有关 JSON 语法和属性，请参阅 [Microsoft.Logic 资源类型](/azure/templates/microsoft.logic/allversions)。

有关 Azure 资源管理器模板的详细信息，请参阅以下文章：

* [创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)
* [开发用于实现云一致性的 Azure 资源管理器模板](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>逻辑应用结构

逻辑应用定义具有以下基本步骤，您可以从"视图设计器"切换到"代码视图"或通过使用一种工具，如[Azure 资源浏览器](http://resources.azure.com)。 逻辑应用定义使用 Javascript 对象表示法 (JSON)，因此 JSON 语法和属性的详细信息，请参阅[Microsoft.Logic 资源类型](/azure/templates/microsoft.logic/allversions)。

* **逻辑应用资源**：描述在逻辑应用的位置或区域，如定价计划和工作流定义的信息。

* **工作流定义**：介绍在逻辑应用的触发器和操作以及如何在 Azure 逻辑应用服务运行工作流。 可以在代码视图中，找到工作流定义在`definition`部分。

* **连接**：如果在逻辑应用中，使用托管的连接器`$connections`部分引用其他资源，用于安全地存储在逻辑应用和其他系统或服务，如连接字符串和访问令牌之间的这些连接有关的元数据。 在逻辑应用定义，对这些连接的引用出现在逻辑应用定义的内部`parameters`部分。

若要创建可用于 Azure 资源组部署的逻辑应用模板，必须定义的资源，并根据需要参数化。 例如，如果部署到开发、测试和生产环境，则可能想要在每个环境中使用不同的连接字符串连接到 SQL 数据库。
或者，可能想要在不同的订阅或资源组中进行部署。

## <a name="create-logic-app-deployment-templates"></a>创建逻辑应用部署模板

若要创建有效的逻辑应用部署模板最简单方法，针对 Visual Studio 扩展中使用 Visual Studio 和 Azure 逻辑应用工具。 通过将逻辑应用从 Azure 门户下载到 Visual Studio，可以获得可用于任何 Azure 订阅和位置的有效部署模板。 此外，下载逻辑应用会自动参数化嵌入在模板中的逻辑应用定义。
有关创建和管理 Visual Studio 中的逻辑应用的详细信息，请参阅[使用 Visual Studio 创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)并[使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。

Visual Studio 或手动创建模板和所需的参数的本主题中的指南，以外还可以使用[用于创建逻辑应用模板 PowerShell 模块](https://github.com/jeffhollan/LogicAppTemplateCreator)。 此开放源代码模块首先评估逻辑应用和逻辑应用使用任何连接。 该模块然后生成具有部署的必需参数的模板资源。 例如，假设有一个逻辑应用从 Azure 服务总线队列接收消息并将数据添加到 Azure SQL 数据库。 模块工具保留所有业务流程逻辑并参数化 SQL 和服务总线连接字符串，以便您可以在部署中设置这些值。

> [!IMPORTANT]
> 在逻辑应用所在的同一个 Azure 资源组中必须存在连接。
> 为用于任何 Azure 租户和订阅的访问令牌，使用的模块的 PowerShell 模块[Azure 资源管理器客户端工具](https://github.com/projectkudu/ARMClient)。 有关详细信息，请参阅此[有关 Azure 资源管理器客户端工具的文章](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)更详细地讨论了 ARMClient。

### <a name="install-powershell-module-for-logic-app-templates"></a>安装适用于逻辑应用模板 PowerShell 模块

有关安装的模块的最简单办法[PowerShell 库](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)，使用以下命令：

`Install-Module -Name LogicAppTemplate`

您还可以手动安装 PowerShell 模块：

1. 下载最新[逻辑应用模板创建程序](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。

1. 提取文件夹中的 PowerShell 模块文件夹，这通常是`%UserProfile%\Documents\WindowsPowerShell\Modules`。

### <a name="generate-logic-app-template---powershell"></a>生成逻辑应用模板-PowerShell

安装 PowerShell 之后，可以使用以下命令来生成模板：

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 是 Azure 订阅 id。 此命令行首先通过 ARMClient 获取访问令牌，然后将其传输到 PowerShell 脚本，再在 JSON 文件中创建模板。

## <a name="parameters-in-logic-app-templates"></a>逻辑应用模板中的参数

创建在逻辑应用模板后，可以添加和编辑所有必需的参数。 您的模板包含多个`parameters`部分，例如： 

* 在逻辑应用的工作流定义都有自己[`parameters`部分](../logic-apps/logic-apps-workflow-definition-language.md#parameters)可以在其中定义逻辑应用将使用用于接受在部署时输入的所有参数。

* 在 Resource Manager 模板都有自己[`parameters`部分](../azure-resource-manager/resource-group-authoring-templates.md#parameters)单独从逻辑应用的`parameters`部分。 例如：

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

例如，假设逻辑应用定义引用的资源 ID，表示 Azure 函数或嵌套的逻辑应用工作流，并且你想要部署该资源 ID 以及逻辑应用为一个部署。 您可以添加该 ID 作为您的模板中的资源和参数化该 id。 您可以使用这种方法对自定义 Api 或 OpenAPI 终结点的引用 (以前称为"Swagger")，你想要部署每个 Azure 资源组。

当您在部署模板中使用参数时，因此在逻辑应用设计器可以正确显示这些参数遵循以下指南：

* 仅在这些触发器和操作中使用参数：

  * Azure Functions 应用
  * 嵌套或子逻辑应用工作流
  * API 管理调用
  * API 连接运行时 URL
  * API 连接路径

* 在定义参数时，请确保通过使用提供默认值`defaultValue`属性值，例如：

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* 为了保护或隐藏在模板中的敏感信息，请保护你的参数。 详细了解如何[如何使用安全的参数](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)。

下面是一个示例，演示如何参数化的 Azure 服务总线"发送消息"操作：

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

### <a name="reference-dependent-resources"></a>引用从属资源

如果你的逻辑应用需要对从属资源的引用，则可以使用[Azure 资源管理器模板函数](../azure-resource-manager/resource-group-template-functions.md)逻辑应用的部署模板中。 例如，假设您想让逻辑应用来引用 Azure 函数或集成帐户的合作伙伴、 协议和其他所需与逻辑应用一起部署的项目定义。
您可以使用资源管理器模板函数，如`parameters`， `variables`， `resourceId`， `concat`，依次类推。

下面是一个示例，演示如何通过定义这些参数替换 Azure 函数的资源 ID:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

下面是如何引用 Azure 函数时使用这些参数：

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

如果有现有的 Azure 资源组项目，您可以通过使用 JSON 大纲窗口向该项目添加逻辑应用。 还可以添加另一个逻辑应用和之前创建的应用。

1. 在“解决方案资源管理器”中，打开 `<template>.json` 文件。

2. 从**视图**菜单中，选择**其他 Windows** > **JSON 大纲**。

3. 若要将资源添加到模板文件中，选择**添加资源**顶部的 JSON 大纲窗口。 或者在“JSON 大纲”窗口中，右键单击“资源”，并选择“添加新资源”。

   ![“JSON 大纲”窗口](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. 在“添加资源”对话框中，找到并选择“逻辑应用”。 为逻辑应用命名，并选择“添加”。

   ![添加资源](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>获取支持

有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署逻辑应用模板](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
