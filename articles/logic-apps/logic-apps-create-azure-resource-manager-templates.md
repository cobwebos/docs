---
title: 创建用于部署的逻辑应用模板
description: 了解如何在 azure 逻辑应用中创建用于自动部署的 Azure 资源管理器模板
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: d45cf8d951eb542e248a3ff4fe714b4d2af90cc7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428727"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>创建 Azure 资源管理器模板以便自动部署 Azure 逻辑应用

为了帮助你自动创建和部署逻辑应用，本文介绍了可为逻辑应用创建[Azure 资源管理器模板](../azure-resource-manager/management/overview.md)的方式。 有关包含工作流定义和部署所需的其他资源的模板的结构和语法的概述，请参阅[概述：使用 Azure 资源管理器模板自动部署逻辑应用](logic-apps-azure-resource-manager-templates-overview.md)。

Azure 逻辑应用提供了一个[预建的逻辑应用 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，你可以重复使用该模板来创建逻辑应用，还可以定义用于部署的资源和参数。 可以将此模板用于自己的业务方案，也可以根据需要自定义此模板。

> [!IMPORTANT]
> 请确保模板中的连接与逻辑应用使用相同的 Azure 资源组和位置。

有关 Azure 资源管理器模板的详细信息，请参阅以下主题：

* [Azure 资源管理器模板结构和语法](../azure-resource-manager/templates/template-syntax.md)
* [创作 Azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)
* [开发用于实现云一致性的 Azure 资源管理器模板](../azure-resource-manager/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>用 Visual Studio 创建模板

若要创建最适合部署的有效参数化逻辑应用模板，请使用 Visual Studio （免费社区版或更高版本）以及适用于 Visual Studio 的 Azure 逻辑应用工具。 然后，你可以[在 Visual studio 中创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) [，或者查找 Azure 门户中的现有逻辑应用并将其下载到 Visual studio](../logic-apps/manage-logic-apps-with-visual-studio.md)。

下载逻辑应用后，你将获得一个模板，其中包含逻辑应用和其他资源（如连接）的定义。 模板还*参数化*或定义用于部署逻辑应用和其他资源的值的参数。 可以在单独的参数文件中提供这些参数的值。 这样，你就可以根据你的部署需要更轻松地更改这些值。 有关详细信息，请参阅以下主题：

* [使用 Visual Studio 创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>创建具有 Azure PowerShell 的模板

可以通过将 Azure PowerShell 与[LogicAppTemplate 模块](https://github.com/jeffhollan/LogicAppTemplateCreator)一起使用来创建资源管理器模板。 此开源模块首先计算逻辑应用和逻辑应用使用的任何连接。 然后，该模块会生成模板资源，其中包含部署所必需的参数。

例如，假设有一个逻辑应用，该应用从 Azure 服务总线队列接收消息，并将数据上传到 Azure SQL 数据库。 模块将保留所有业务流程逻辑，并参数化 SQL 和服务总线连接字符串，以便可以根据部署需要提供和更改这些值。

这些示例演示如何使用 Azure 资源管理器模板、Azure Pipelines 在 Azure DevOps 中创建和部署逻辑应用，并 Azure PowerShell：

* [示例：从 Azure 逻辑应用连接到 Azure 服务总线队列](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到 Azure 存储帐户](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到集成帐户](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>安装 PowerShell 模块

1. 如果尚未安装，请安装[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

1. 若要从[PowerShell 库](https://www.powershellgallery.com/packages/LogicAppTemplate)最简单的方法安装 LogicAppTemplate 模块，请运行以下命令：

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   若要更新到最新版本，请运行以下命令：

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

或者，若要手动安装，请遵循适用于[逻辑应用模板创建者](https://github.com/jeffhollan/LogicAppTemplateCreator)的 GitHub 中的步骤。

### <a name="install-azure-resource-manager-client"></a>安装 Azure 资源管理器客户端

若要使 LogicAppTemplate 模块可与任何 Azure 租户和订阅访问令牌一起使用，请安装[azure 资源管理器客户端工具](https://github.com/projectkudu/ARMClient)，该工具是一个调用 AZURE 资源管理器 API 的简单命令行工具。

使用此工具运行 `Get-LogicAppTemplate` 命令时，该命令将首先通过 ARMClient 工具获取一个访问令牌，通过管道将令牌传递给 PowerShell 脚本，并将该模板创建为 JSON 文件。 有关该工具的详细信息，请参阅[有关 Azure 资源管理器客户端工具的文章](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)。

### <a name="generate-template-with-powershell"></a>通过 PowerShell 生成模板

若要在安装 LogicAppTemplate 模块并[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)后生成模板，请运行以下 PowerShell 命令：

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

若要根据[azure 资源管理器客户端工具](https://github.com/projectkudu/ARMClient)中的令牌进行管道的建议，请改为运行此命令，其中 `$SubscriptionId` 是你的 AZURE 订阅 ID：

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

提取后，可以通过运行以下命令从模板创建参数文件：

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

若要提取 Azure Key Vault 引用（仅限静态），请运行以下命令：

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| 参数 | 需要 | Description |
|------------|----------|-------------|
| TemplateFile | 是 | 模板文件的文件路径 |
| KeyVault | 否 | 描述如何处理可能的 key vault 值的枚举。 默认为 `None`。 |
||||

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署逻辑应用模板](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
