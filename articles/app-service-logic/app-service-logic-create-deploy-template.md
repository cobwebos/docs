---
title: "创建逻辑应用部署模板 | Microsoft Docs"
description: "了解如何创建逻辑应用的部署模板，并将其用于发布管理"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: a0580f8d303c7ce33a65f0ce6faecf2492f851b0
ms.openlocfilehash: d0015760fe086b00a264aabd38cc2625f55f4926


---
# <a name="create-a-logic-app-deployment-template"></a>创建逻辑应用的部署模板
创建逻辑应用之后，可能想要将其创建为 Azure Resource Manager 模板。 这样，可以轻松地将逻辑应用部署到任何需要它的环境或资源组。 有关 Resource Manager 模板的简介，请务必查阅文章[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)和[使用 Azure Resource Manager 模板部署资源](../azure-resource-manager/resource-group-template-deploy.md)。

## <a name="logic-app-deployment-template"></a>逻辑应用的部署模板
逻辑应用具有三个基本组件：

* **逻辑应用资源**。 此资源包含有关定价计划、位置和工作流定义等信息。
* **工作流定义**。 这是在代码视图中看到的内容。 它包括流的步骤和引擎如何执行这些步骤的定义。 这是逻辑应用资源的 `definition` 属性。
* **连接**。 这些是单独的资源，用于安全存储有关任何连接器连接的元数据，例如连接字符串和访问令牌。 在逻辑应用资源的 `parameters` 部分中引用了逻辑应用中的这些资源。

可以使用诸如 [Azure 资源管理器](http://resources.azure.com)的工具查看现有的逻辑应用的所有这些资源。

若要创建逻辑应用模板以用于资源组部署，需要先定义资源并根据需要将其参数化。 例如，如果部署到开发、测试和生产环境，则可能想要在每个环境中使用 SQL 数据库的不同连接字符串。 或者，可能想要在不同的订阅或资源组中进行部署。  

## <a name="create-a-logic-app-deployment-template"></a>创建逻辑应用的部署模板
获得有效逻辑应用部署模板的最简单方法是使用 [Visual Studio Tools for Logic Apps](app-service-logic-deploy-from-vs.md)。  Visual Studio 工具生成可跨任何订阅或位置使用的有效部署模板。

其他一些工具可在你创建逻辑应用部署模板时提供帮助。 你可以手动创建，也就是使用此处讨论的资源根据需要创建参数。 另一种方法是使用[逻辑应用模板创建程序](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell 模块。 此开放源代码模块首先评估逻辑应用和其使用的任何连接，然后生成用于部署的模板资源与所需参数。 例如，如果逻辑应用从 Azure 服务总线队列接收消息，并将数据添加到 Azure SQL 数据库，那么该工具将保存所有业务流程逻辑并参数化 SQL 和服务总线连接字符串，以便可以在部署中设置它们。

> [!NOTE]
> 连接必须位于和逻辑应用一样的资源组中。
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>安装逻辑应用模板 PowerShell 模块
安装此模块的最简单方法是通过 [PowerShell 库](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)中的命令 `Install-Module -Name LogicAppTemplate` 进行安装。  

你也可以手动安装 PowerShell 模块：

1. 下载最新版本的[逻辑应用模板创建程序](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。  
2. 提取 PowerShell 模块文件夹中的文件夹（通常为 `%UserProfile%\Documents\WindowsPowerShell\Modules`）。

为了使模块可与任何租户和订阅访问令牌一起使用，我们建议通过 [ARMClient](https://github.com/projectkudu/ARMClient) 命令行工具来使用它。  此[博客文章](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)详细讨论了 ARMClient。

### <a name="generate-a-logic-app-template-by-using-powershell"></a>使用 PowerShell 来生成逻辑应用模板
安装 PowerShell 之后，可以使用以下命令来生成模板：

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 是 Azure 订阅 ID。 此命令行首先通过 ARMClient 获取访问令牌，然后将其传输到 PowerShell 脚本，再在 JSON 文件中创建模板。

## <a name="add-parameters-to-a-logic-app-template"></a>向逻辑应用模板添加参数
创建逻辑应用模板后，可以继续添加或修改可能需要的参数。 例如，如果定义在 Azure 函数或嵌套工作流中包括资源 ID，并且打算在单个部署中部署此函数或工作流，那么可以将更多资源添加到模板，并根据需要参数化 ID。 上述情况同样适用于对要与每个资源组一起部署的自定义 API 或 Swagger 终结点的任何引用。

## <a name="deploy-a-logic-app-template"></a>部署逻辑应用模板
可以使用任意数量的工具来部署模板，包括 PowerShell、REST API、Visual Studio Release Management 和 Azure 门户模板部署。 要获取更多信息，请参阅有关[使用 Azure Resource Manager 模板部署资源](../azure-resource-manager/resource-group-template-deploy.md)的文章。 另外，我们建议创建[参数文件](../azure-resource-manager/resource-group-template-deploy.md#parameters)来存储参数的值。

### <a name="authorize-oauth-connections"></a>授权 OAuth 连接
部署后，逻辑应用使用有效参数进行端到端的运行。 但是，仍需要对 OAuth 连接授权以生成有效的访问令牌。 要执行此操作，可以在设计器中打开逻辑应用，然后授权连接。 或者，如果想要自动执行，可以使用脚本来同意每个 OAuth 连接。 在 GitHub 的 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 项目下有一个示例脚本。

## <a name="visual-studio-release-management"></a>Visual Studio Release Management
部署和管理环境的一个常用方案是将 Visual Studio Release Management 之类的工具用于逻辑应用部署模板。 Visual Studio Team Services 包括可以添加到任何内部版本或发布管道的[部署 Azure 资源组](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup)任务。 授权需要有[服务主体](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)才能部署，然后生成发布定义。

1. 在 Release Management 中若要创建新的定义，请选择“空”来首先创建一个空定义。

    ![创建新的空定义][1]   
2. 选择该定义所需的任何资源。 可能是手动生成或生成过程中生成的逻辑应用模板。
3. 添加“Azure 资源组部署”任务。
4. 使用[服务主体](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)进行配置，并引用模板和模板参数文件。
5. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG



<!--HONumber=Dec16_HO3-->


