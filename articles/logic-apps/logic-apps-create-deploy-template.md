---
title: 为 Azure 逻辑应用创建部署模板 | Microsoft Docs
description: 使用 Azure 资源管理器模板部署逻辑应用
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 647ffeb05542e12d19cefa3fa0dbf55e5585109a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297907"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>使用 Azure 资源管理器模板部署逻辑应用

创建逻辑应用之后，可能想要将其创建为 Azure 资源管理器模板。
这样，可以轻松地将逻辑应用部署到任何需要它的环境或资源组。
有关资源管理器模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)和[使用 Azure 资源管理器模板部署资源](../azure-resource-manager/resource-group-template-deploy.md)。

## <a name="logic-app-deployment-template"></a>逻辑应用的部署模板

逻辑应用具有三个基本组件：

* **逻辑应用资源**：包含定价计划、位置和工作流定义等内容的相关信息。
* **工作流定义**：描述逻辑应用的工作流步骤以及逻辑应用引擎应如何执行工作流。
可以在逻辑应用的“代码视图”窗口中查看此定义。
在逻辑应用资源中，可以在 `definition` 属性中找到此定义。
* **连接**：指单独的资源，用于安全存储有关任何连接器连接的元数据，例如连接字符串和访问令牌。
在逻辑应用资源中，逻辑应用在 `parameters` 节引用这些资源。

可以使用诸如 [Azure 资源管理器](http://resources.azure.com)之类的工具查看现有逻辑应用的所有这些资源。

要创建逻辑应用模板以用于资源组部署，必须先定义资源并根据需要将其参数化。
例如，如果部署到开发、测试和生产环境，则可能想要在每个环境中使用不同的连接字符串连接到 SQL 数据库。
或者，可能想要在不同的订阅或资源组中进行部署。  

## <a name="create-a-logic-app-deployment-template"></a>创建逻辑应用的部署模板

获得有效逻辑应用部署模板的最简单方法是使用 [Visual Studio Tools for Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)。
Visual Studio 工具生成可跨任何订阅或位置使用的有效部署模板。

其他一些工具可在创建逻辑应用部署模板时提供帮助。
可以手动创建，也就是使用此处讨论的资源根据需要创建参数。
另一种方法是使用[逻辑应用模板创建程序](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 模块。 此开放源代码模块首先评估逻辑应用及它使用的任何连接，然后使用必要的部署参数生成模板资源。
例如，如果逻辑应用从 Azure 服务总线队列接收消息，并将数据添加到 Azure SQL 数据库，那么该工具将保存所有业务流程逻辑并参数化 SQL 和服务总线连接字符串，以便可以在部署中设置它们。

> [!NOTE]
> 连接必须位于和逻辑应用一样的资源组中。
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>安装逻辑应用模板 PowerShell 模块
安装此模块的最简单方法是通过 [PowerShell 库](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)中的命令 `Install-Module -Name LogicAppTemplate` 进行安装。  

也可以手动安装 PowerShell 模块：

1. 下载最新版本的[逻辑应用模板创建程序](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。  
2. 提取 PowerShell 模块文件夹中的文件夹（通常为 `%UserProfile%\Documents\WindowsPowerShell\Modules`）。

为了使模块可与任何租户和订阅访问令牌一起使用，建议通过 [ARMClient](https://github.com/projectkudu/ARMClient) 命令行工具来使用它。  此[博客文章](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)详细讨论了 ARMClient。

### <a name="generate-a-logic-app-template-by-using-powershell"></a>使用 PowerShell 来生成逻辑应用模板
安装 PowerShell 之后，可以使用以下命令来生成模板：

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 是 Azure 订阅 ID。 此命令行首先通过 ARMClient 获取访问令牌，然后将其传输到 PowerShell 脚本，再在 JSON 文件中创建模板。

## <a name="add-parameters-to-a-logic-app-template"></a>向逻辑应用模板添加参数
创建逻辑应用模板后，可以继续添加或修改可能需要的参数。 例如，如果定义在 Azure 函数或嵌套工作流中包括资源 ID，并且打算在单个部署中部署此函数或工作流，那么可以将更多资源添加到模板，并根据需要参数化 ID。 上述情况同样适用于对要与每个资源组一起部署的自定义 API 或 Swagger 终结点的任何引用。

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>将从属资源的引用添加到 Visual Studio 部署模板

如果希望逻辑应用引用从属资源，可以在逻辑应用部署模板中使用 [Azure 资源管理器模板函数](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)。 例如，可能想让逻辑应用引用要与逻辑应用一起部署的 Azure 函数或集成帐户。 请遵循以下准则，了解如何在部署模板中使用参数，使逻辑应用设计器可正确地呈现。 

可以在以下类型的触发器和操作中使用逻辑应用参数：

*   子工作流
*   函数应用
*   APIM 调用
*   API 连接运行时 URL
*   API 连接路径

可以使用模板函数，如参数、变量、资源 ID、concat 等。例如，下面是替换 Azure 函数资源 ID 的方法：

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

使用参数的位置：

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
再举一例，可以参数化服务总线发送消息操作：

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl 是可选的，可从模板中删除（如果存在）。
> 


> [!NOTE] 
> 为了使逻辑应用设计器能工作，在使用参数时必须提供默认值，例如：
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>将逻辑应用添加到现有资源组项目

如果存在现有资源组项目，可在“JSON 大纲”窗口中将逻辑应用添加到该项目。 还可以添加另一个逻辑应用和之前创建的应用。

1. 打开 `<template>.json` 文件。

2. 若要打开“JSON 大纲”窗口，请转到“视图” > “其他窗口” > “JSON 大纲”。

3. 要将资源添加到模板文件，请在“JSON 大纲”窗口顶部单击“添加资源”。 或者在“JSON 大纲”窗口中，右键单击“资源”，并选择“添加新资源”。

    ![“JSON 大纲”窗口](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. 在“添加资源”对话框中，找到并选择“逻辑应用”。 为逻辑应用命名，并选择“添加”。

    ![添加资源](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>部署逻辑应用模板

可以使用任意工具来部署模板，比如 PowerShell、REST API、[Visual Studio Team Services Release Management](#team-services) 和 Azure 门户模板部署。
另外，若要存储参数的值，建议创建[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)。
了解如何[使用 Azure 资源管理器模板和 PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)或[使用 Azure 资源管理器模板和 Azure 门户部署资源](../azure-resource-manager/resource-group-template-deploy-portal.md)。

### <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署后，逻辑应用使用有效参数进行端到端的运行。
但是，仍需要对 OAuth 连接授权以生成有效的访问令牌。
要对 OAuth 连接授权，请在逻辑应用设计器中打开逻辑应用，并对这些连接授权。 或者，如果想要自动部署，可以使用脚本来许可每个 OAuth 连接。
在 GitHub 的 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 项目下有一个示例脚本。

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

部署和管理环境的一个常用方案是将 Visual Studio Team Services Release Management 之类的工具用于逻辑应用部署模板。 Visual Studio Team Services 包括可以添加到任何内部版本或发布管道的[部署 Azure 资源组](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup)任务。 授权需要有[服务主体](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)才能部署，并生成发布定义。

1. 在 Release Management 中，选择“空”以便创建一个空定义。

    ![创建空定义][1]

2. 选择该定义所需的任何资源，最有可能包括手动生成或生成过程中生成的逻辑应用模板。
3. 添加“Azure 资源组部署”任务。
4. 使用[服务主体](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)进行配置，并引用模板和模板参数文件。
5. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
