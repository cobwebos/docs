---
title: 部署逻辑应用模板
description: 了解如何部署为 Azure 逻辑应用创建的 Azure 资源管理器模板
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 662f8c2a4184e858d33dc40effed363ef3ec17e5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904952"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>部署 Azure 逻辑应用的 Azure 资源管理器模板

为逻辑应用创建 Azure 资源管理器模板之后，可以通过以下方式部署模板：

* [Azure 门户](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure 资源管理器 REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>通过 Azure 门户部署

若要将逻辑应用模板自动部署到 Azure，你可以选择以下 "**部署到 azure** " 按钮，该按钮会登录到 Azure 门户并提示你输入逻辑应用的相关信息。 然后，你可以对逻辑应用模板或参数进行任何必要的更改。

[![部署到 Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

例如，登录到 Azure 门户后，系统会提示输入以下信息：

* Azure 订阅名称
* 要使用的资源组
* 逻辑应用位置
* 逻辑应用的名称
* 测试 URI
* 接受指定的条款和条件

有关详细信息，请参阅以下主题：

* [概述：通过 Azure 资源管理器模板自动部署逻辑应用](logic-apps-azure-resource-manager-templates-overview.md)
* [用 Azure 资源管理器模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 进行部署

若要从使用 Visual Studio 创建的 Azure 资源组项目部署逻辑应用模板，请执行以下[步骤以手动将逻辑应用部署](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)到 azure。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署到特定的*Azure 资源组*，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要部署到特定的*Azure 资源组*，请使用以下命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>用 Azure DevOps 部署

若要部署逻辑应用模板并管理环境，团队通常使用[Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)中的工具，如[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) 。 Azure Pipelines 提供[Azure 资源组部署任务](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)，你可以将其添加到任何生成或发布管道。 为了获得部署和生成发布管道的授权，还需要 Azure Active Directory （AD）[服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 详细了解如何将[服务主体与 Azure Pipelines 结合使用](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。

有关包含 Azure Pipelines 的 Azure 资源管理器模板持续集成和持续部署（CI/CD）的详细信息，请参阅以下主题和示例：

* [将资源管理器模板与 Azure Pipelines 集成](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教程：与 Azure Pipelines 的 Azure 资源管理器模板持续集成](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [示例：从 azure 逻辑应用连接到 Azure 服务总线队列，并通过 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 azure 逻辑应用连接到 azure 存储帐户，并通过 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作，并使用 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 azure 逻辑应用连接到集成帐户，并使用 Azure DevOps 中的 Azure Pipelines 进行部署](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

下面是使用 Azure Pipelines 的一般高级步骤：

1. 在 Azure Pipelines 中，创建一个空管道。

1. 选择管道所需的资源，例如逻辑应用模板和模板参数文件，您可以手动生成这些资源，也可以将其作为生成过程的一部分生成。

1. 对于代理作业，查找并添加**Azure 资源组部署**任务。

   ![添加 "Azure 资源组部署" 任务](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用[服务主体](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)进行配置。

1. 添加对逻辑应用模板和模板参数文件的引用。

1. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署完成后，逻辑应用使用有效的参数以端到端的方式工作。 但是，你仍必须对任何 OAuth 连接授权以生成有效的访问令牌，以便对[凭据进行身份验证](../active-directory/develop/authentication-scenarios.md)。 可以通过以下方法来授权 OAuth 连接：

* 对于自动部署，您可以使用为每个 OAuth 连接提供许可的脚本。 下面是 GitHub 中[LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth)项目的示例脚本。

* 若要手动授权 OAuth 连接，请在逻辑应用设计器中打开逻辑应用，不管是在 Azure 门户中还是在 Visual Studio 中。 在设计器中，授权任何所需的连接。

如果改为使用 Azure Active Directory （Azure AD）[服务主体](../active-directory/develop/app-objects-and-service-principals.md)来授权连接，请了解如何[在逻辑应用模板中指定服务主体参数](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
