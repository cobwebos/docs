---
title: 使用 Azure 资源管理器模板部署逻辑应用 - Azure 逻辑应用
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
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681725"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板部署逻辑应用

创建用于部署逻辑应用的 Azure 资源管理器模板后，可按以下方式部署该模板：

* [Azure 门户](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure 资源管理器 REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>通过 Azure 门户进行部署

若要自动将逻辑应用模板部署到 Azure，可以选择下面的“部署到 Azure”按钮，这样可以登录到 Azure 门户，系统会提示你输入有关逻辑应用的信息。 然后，可对逻辑应用模板或参数进行任何必要的更改。

[![部署到 Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，在登录到 Azure 门户后，系统会提示输入以下信息：

* Azure 订阅名称
* 要使用的资源组
* 逻辑应用位置
* 逻辑应用的名称
* 测试 URI
* 接受指定的条款和条件

有关详细信息，请参阅[使用 Azure 资源管理器模板和 Azure 门户部署资源](../azure-resource-manager/resource-group-template-deploy-portal.md)。

## <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署后，逻辑应用使用有效参数进行端到端的运行。 但是，仍需要对 OAuth 连接授权以生成有效的访问令牌。 若要进行自动化部署，可以使用同意建立每个 OAuth 连接的脚本，例如 [GitHub LogicAppConnectionAuth 项目中的此示例脚本](https://github.com/logicappsio/LogicAppConnectionAuth)。 也可以通过 Azure 门户，或者在 Visual Studio 的逻辑应用设计器中打开逻辑应用，来为 OAuth 连接授权。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署到特定的 Azure 资源组，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

若要部署到特定的 Azure 订阅，请使用以下命令：

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要部署到特定的 Azure 资源组，请使用以下命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

若要部署到特定的 Azure 订阅，请使用以下命令：

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题： 

* [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 进行部署

若要部署逻辑应用模板并管理环境，团队通常会使用 [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) 中的 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) 等工具。 Azure Pipelines 提供一个可以添加到任何生成管道或发布管道的 [Azure 资源组部署任务](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)。
若要授权部署和生成发布管道，还需要一个 Azure Active Directory (AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 详细了解如何[在 Azure Pipelines 中使用服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。 

下面是有关使用 Azure Pipelines 的一般性概要步骤：

1. 在 Azure Pipelines 中创建空管道。

1. 选择管道所需的资源，例如，手动生成的或者在生成过程中生成的逻辑应用模板和模板参数文件。

1. 对于代理作业，请找到并添加“Azure 资源组部署”任务。

   ![添加“Azure 资源组部署”任务](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. 使用[服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)进行配置。 

1. 添加对逻辑应用模板和模板参数文件的引用。

1. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

## <a name="get-support"></a>获取支持

有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)
