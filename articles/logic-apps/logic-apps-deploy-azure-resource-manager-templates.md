---
title: 部署逻辑应用模板
description: 了解如何为 Azure 逻辑应用部署已创建的 Azure 资源管理器模板
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 81fad94dc02bd57f839d7ab8653bec7074e41800
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076334"
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

若要自动将逻辑应用模板部署到 Azure，可以选择下面的“部署到 Azure”按钮，这样可以登录到 Azure 门户，系统会提示你输入有关逻辑应用的信息。 然后，可对逻辑应用模板或参数进行任何必要的更改。

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

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要部署到特定的 Azure 资源组，请使用以下命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 进行部署

若要部署逻辑应用模板并管理环境，团队通常会使用 [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services) 中的 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 等工具。 Azure Pipelines 提供一个可以添加到任何生成管道或发布管道的 [Azure 资源组部署任务](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)。 若要授权部署和生成发布管道，还需要一个 Azure Active Directory (AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 详细了解如何[在 Azure Pipelines 中使用服务主体](/azure/devops/pipelines/library/connect-to-azure)。

若要详细了解如何使用 Azure Pipelines 进行与 Azure 资源管理器模板相对应的持续集成和持续部署 (CI/CD)，请参阅以下主题和示例：

* [将资源管理器模板与 Azure Pipelines 集成](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [示例：从 Azure 逻辑应用连接到 Azure 服务总线队列并在 Azure DevOps 中使用 Azure Pipelines 进行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到 Azure 存储帐户并在 Azure DevOps 中使用 Azure Pipelines 进行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作并在 Azure DevOps 中使用 Azure Pipelines 进行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到集成帐户并在 Azure DevOps 中使用 Azure Pipelines 进行部署](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [示例：使用 Azure 逻辑应用协调 Azure Pipelines](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

下面是有关使用 Azure Pipelines 的一般性概要步骤：

1. 在 Azure Pipelines 中创建空管道。

1. 选择管道所需的资源，例如，手动生成的或者在生成过程中生成的逻辑应用模板和模板参数文件。

1. 对于代理作业，请找到并添加“Azure 资源组部署”任务。

   ![添加“Azure 资源组部署”任务](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用[服务主体](/azure/devops/pipelines/library/connect-to-azure)进行配置。

1. 添加对逻辑应用模板和模板参数文件的引用。

1. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署完成后，逻辑应用使用有效的参数以端到端的方式运行，但为了生成用于 [验证凭据](../active-directory/develop/authentication-vs-authorization.md)的有效访问令牌，你仍需要授权或使用预授权 OAuth 连接。 但是，你只需部署和验证 API 连接资源一次，这意味着在后续部署中无需包含这些连接资源，除非你需要更新连接信息。 如果使用持续集成和连续部署管道，则仅部署已更新的逻辑应用资源，无需每次都重新授权连接。

下面是一些处理授权连接的建议：

* 在同一区域中的逻辑应用上 Preauthorize 和共享 API 连接资源。 API 连接与逻辑应用独立于 Azure 资源。 逻辑应用与 API 连接资源存在依赖关系，而 API 连接资源不依赖于逻辑应用，并且在删除从属逻辑应用后仍保留。 此外，逻辑应用可以使用其他资源组中存在的 API 连接。 但是，逻辑应用设计器仅支持在与逻辑应用相同的资源组中创建 API 连接。

  > [!NOTE]
  > 如果要考虑共享 API 连接，请确保解决方案可以 [处理潜在的限制问题](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling)。 阻止在连接级别进行，因此跨多个逻辑应用重用相同的连接可能会增加限制问题的可能性。

* 除非你的方案涉及到需要多重身份验证的服务和系统，否则，你可以使用 PowerShell 脚本为每个 OAuth 连接提供许可，方法是在具有活动浏览器会话以及已提供授权的活动浏览器会话的虚拟机上运行一个普通用户帐户。 例如，你可以重新使用 [逻辑应用 GitHub 存储库中的 LogicAppConnectionAuth 项目](https://github.com/logicappsio/LogicAppConnectionAuth)提供的示例脚本。

* 通过在逻辑应用设计器中打开逻辑应用，在 Azure 门户或 Visual Studio 中打开逻辑应用，手动授权 OAuth 连接。

* 如果改用 Azure Active Directory (Azure AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)来授权连接，请了解如何[在逻辑应用模板中指定服务主体参数](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
