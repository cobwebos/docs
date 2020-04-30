---
title: 部署逻辑应用模板
description: 了解如何为 Azure 逻辑应用部署已创建的 Azure 资源管理器模板
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086430"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>为 Azure 逻辑应用部署 Azure 资源管理器模板

创建用于逻辑应用的 Azure 资源管理器模板后，可按以下方式部署该模板：

* [Azure 门户](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>通过 Azure 门户进行部署

若要自动将逻辑应用模板部署到 Azure，可以选择下面的“部署到 Azure”按钮，这样可以登录到 Azure 门户，系统会提示你输入有关逻辑应用的信息。  然后，可对逻辑应用模板或参数进行任何必要的更改。

[![“部署到 Azure”](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，在登录到 Azure 门户后，系统会提示输入以下信息：

* Azure 订阅名称
* 要使用的资源组
* 逻辑应用位置
* 逻辑应用的名称
* 测试 URI
* 接受指定的条款和条件

有关详细信息，请参阅以下主题：

* [概述：使用 Azure 资源管理器模板自动完成逻辑应用的部署](logic-apps-azure-resource-manager-templates-overview.md)
* [使用 Azure 资源管理器模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 进行部署

若要从使用 Visual Studio 创建的 Azure 资源组项目部署逻辑应用模板，请按[这些步骤](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)操作，将逻辑应用手动部署到 Azure。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署到特定的 Azure 资源组，请使用以下命令： 

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要部署到特定的 Azure 资源组，请使用以下命令： 

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 进行部署

若要部署逻辑应用模板并管理环境，团队通常会使用 [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) 中的 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) 等工具。 Azure Pipelines 提供一个可以添加到任何生成管道或发布管道的 [Azure 资源组部署任务](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)。 若要授权部署和生成发布管道，还需要一个 Azure Active Directory (AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 详细了解如何[在 Azure Pipelines 中使用服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。

若要详细了解如何使用 Azure Pipelines 进行与 Azure 资源管理器模板相对应的持续集成和持续部署 (CI/CD)，请参阅以下主题和示例：

* [将资源管理器模板与 Azure Pipelines 集成](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [示例：从 Azure 逻辑应用连接到 Azure 服务总线队列并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到 Azure 存储帐户并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到集成帐户并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [示例：使用 Azure 逻辑应用协调 Azure Pipelines](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

下面是有关使用 Azure Pipelines 的一般性概要步骤：

1. 在 Azure Pipelines 中创建空管道。

1. 选择管道所需的资源，例如，手动生成的或者在生成过程中生成的逻辑应用模板和模板参数文件。

1. 对于代理作业，请找到并添加“Azure 资源组部署”任务。****

   ![添加“Azure 资源组部署”任务](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用[服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)进行配置。

1. 添加对逻辑应用模板和模板参数文件的引用。

1. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署后，逻辑应用将使用有效参数进行端到端的运行。 但是，仍然必须授权任何 OAuth 连接，以生成用于[对凭据进行身份验证](../active-directory/develop/authentication-scenarios.md)的有效访问令牌。 下面是可以用来授权 OAuth 连接的方式：

* 若要进行自动部署，可以使用脚本为每个 OAuth 连接提供许可。 下面是 GitHub 中的一个示例脚本，位于 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) 项目中。

* 若要手动授权 OAuth 连接，请在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 在设计器中，授权任何必需的连接。

如果改用 Azure Active Directory (Azure AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)来授权连接，请了解如何[在逻辑应用模板中指定服务主体参数](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
