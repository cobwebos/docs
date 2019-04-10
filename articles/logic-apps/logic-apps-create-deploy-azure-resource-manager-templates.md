---
title: 部署逻辑应用与 Azure 资源管理器模板-Azure 逻辑应用
description: 使用 Azure 资源管理器模板部署逻辑应用
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: bbb10bf0174b6e06e28d171510345ed92b6642d9
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357080"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>部署逻辑应用使用 Azure 资源管理器模板

创建用于部署逻辑应用的 Azure 资源管理器模板后，可以部署以下方式在模板：

* [Azure 门户](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure 资源管理器 REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>通过 Azure 门户进行部署

若要自动部署到 Azure 逻辑应用模板，可以选择以下**部署到 Azure**按钮，登录到 Azure 门户，并提示您输入有关逻辑应用的信息。 然后可以对逻辑应用模板或参数进行任何必要的更改。

[![D部署到 Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，系统提示输入此信息后登录到 Azure 门户：

* Azure 订阅名称
* 要使用的资源组
* 逻辑应用位置
* 逻辑应用的名称
* 测试 URI
* 接受指定的条款和条件

有关详细信息，请参阅[使用 Azure 资源管理器模板和 Azure 门户部署资源](../azure-resource-manager/resource-group-template-deploy-portal.md)。

## <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署后，逻辑应用使用有效参数进行端到端的运行。 但是，仍需要对 OAuth 连接授权以生成有效的访问令牌。 对于自动化部署，可以使用同意每个 OAuth 连接，这样的脚本[GitHub LogicAppConnectionAuth 项目中的示例脚本](https://github.com/logicappsio/LogicAppConnectionAuth)。 您可以在逻辑应用设计器中打开逻辑应用为通过 Azure 门户或 Visual Studio 中的 OAuth 连接授权。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要将部署到特定*Azure 资源组*，使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

若要部署到特定 Azure 订阅，请使用以下命令：

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要将部署到特定*Azure 资源组*，使用以下命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

若要部署到特定 Azure 订阅，请使用以下命令：

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题： 

* [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 部署

若要将逻辑应用模板部署和管理环境，团队通常使用一种工具如[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines)中[Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)。 Azure 管道提供[Azure 资源组部署任务](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)，可以将添加到任何生成或发布管道。
用于部署和生成发布管道的授权，还需要 Azure Active Directory (AD)[服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 详细了解如何[包含 Azure 管道，使用服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。 

有关使用 Azure 管道的常规高级步骤如下：

1. 在 Azure 管道创建一个空的管道。

1. 选择所需的管道，如逻辑应用模板和模板参数文件，手动或作为生成过程的一部分生成的资源。

1. 代理作业，查找并添加**Azure 资源组部署**任务。

   ![添加"Azure 资源组部署"任务](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. 配置与[服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。 

1. 添加到逻辑应用模板和模板参数文件的引用。

1. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

## <a name="get-support"></a>获取支持

有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)
