---
title: 为部署创建逻辑应用模板 - Azure 逻辑应用
description: 了解如何在 Azure 逻辑应用中创建用于自动化部署的 Azure 资源管理器模板
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 57e9cec16326068cc7de74b8f7266fbe47808fed
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845452"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>创建 Azure 资源管理器模板以自动化 Azure 逻辑应用的部署

为了帮助你自动创建和部署逻辑应用，本文介绍了在为逻辑应用创建 [Azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)时可用的方法。 有关包含工作流定义以及用于部署的其他资源的模板的结构和语法概述，请参阅[概述：使用 Azure 资源管理器模板将逻辑应用部署自动化](logic-apps-azure-resource-manager-templates-overview.md)。

Azure 逻辑应用提供一个可以重复使用的[预生成逻辑应用 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，使用它不仅可以创建逻辑应用，还可以定义要对部署使用的资源和参数。 可以将此模板用于自己的业务方案，也可以根据需要自定义此模板。

> [!IMPORTANT]
> 确保模板中的连接使用与逻辑应用相同的 Azure 资源组和位置。

有关 Azure 资源管理器模板的详细信息，请参阅以下主题：

* [Azure 资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)
* [创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)
* [开发用于实现云一致性的 Azure 资源管理器模板](../azure-resource-manager/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>使用 Visual Studio 创建模板

若要创建最适合部署的有效参数化逻辑应用模板，请使用 Visual Studio（免费的社区版或更高版本）以及适用于 Visual Studio 的 Azure 逻辑应用工具。 然后，可以[在 Visual Studio 中创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)，或者[在 Azure 门户中找到某个现有逻辑应用并将其下载到 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)。

下载逻辑应用后，你将获得一个模板，其中包含逻辑应用和其他资源（例如连接）的定义。 该模板还会参数化或定义用于部署逻辑应用和其他资源的值的参数。 可在单独的参数文件中提供这些参数的值。 这样，就可以根据部署需求更轻松地更改这些值。 有关详细信息，请参阅以下主题：

* [使用 Visual Studio 创建逻辑应用](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [使用 Visual Studio 管理逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>使用 Azure PowerShell 创建模板

可以结合 [LogicAppTemplate 模块](https://github.com/jeffhollan/LogicAppTemplateCreator)使用 Azure PowerShell 创建资源管理器模板。 此开源模块首先评估逻辑应用，以及逻辑应用使用的任何连接。 然后，该模块生成模板资源以及用于部署的必需参数。

例如，假设某个逻辑应用要从 Azure 服务总线队列接收消息，并将数据上传到 Azure SQL 数据库。 该模块会保留所有业务流程逻辑，并参数化 SQL 和服务总线连接字符串，使你能够根据部署需求提供和更改这些值。

这些示例演示如何使用 Azure 资源管理器模板、Azure DevOps 中的 Azure Pipelines，以及 Azure PowerShell 来创建和部署逻辑应用：

* [示例：从 Azure 逻辑应用连接到 Azure 服务总线队列](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到 Azure 存储帐户](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：为 Azure 逻辑应用设置函数应用操作](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [示例：从 Azure 逻辑应用连接到集成帐户](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>安装 PowerShell 模块

1. 安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)（如果尚未安装）。

1. 若要以最简单的方式从 [PowerShell 库](https://www.powershellgallery.com/packages/LogicAppTemplate)安装 LogicAppTemplate 模块，请运行以下命令：

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   若要更新到最新版本，请运行以下命令：

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

或者，若要手动安装，请遵循 GitHub 中适用于[逻辑应用模板创建者](https://github.com/jeffhollan/LogicAppTemplateCreator)的步骤。

### <a name="install-azure-resource-manager-client"></a>安装 Azure 资源管理器客户端

若要使 LogicAppTemplate 模块能够使用任何 Azure 租户和订阅访问令牌，请安装 [Azure 资源管理器客户端工具](https://github.com/projectkudu/ARMClient)，这是一个可以调用 Azure 资源管理器 API 的简单命令行工具。

使用此工具运行 `Get-LogicAppTemplate` 命令时，该命令首先会通过 ARMClient 工具获取访问令牌，通过管道将该令牌传送到 PowerShell 脚本，然后以 JSON 文件的形式创建模板。 有关该工具的详细信息，请参阅这篇[有关 Azure 资源管理器客户端工具的文章](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)。

### <a name="generate-template-with-powershell"></a>使用 PowerShell 生成模板

若要在安装 LogicAppTemplate 模块并[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)后生成模板，请运行以下 PowerShell 命令：

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

若要根据建议从 [Azure 资源管理器客户端工具](https://github.com/projectkudu/ARMClient)以管道方式传送令牌，请运行以下命令，其中的 `$SubscriptionId` 是你的 Azure 订阅 ID：

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

提取后，可运行以下命令基于模板创建参数文件：

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

对于包含 Azure Key Vault 引用的提取内容（仅限静态值），请运行以下命令：

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parameters | 必填 | 描述 |
|------------|----------|-------------|
| TemplateFile | 是 | 模板文件的文件路径 |
| KeyVault | 否 | 一个枚举，描述如何处理可能的 Key Vault 值。 默认值为 `None`。 |
||||

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署逻辑应用模板](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
