---
title: 使用 Azure 资源管理器模板创建工作区
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 资源管理器模板创建新的 Azure 机器学习服务工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 89c24512e50dedbf10d145088ec77c2e6e303d1e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873154"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>使用 Azure 资源管理器模板创建 Azure 机器学习服务的工作区

本文介绍几种使用 Azure 资源管理器模板创建 Azure 机器学习服务工作区的方法。 使用资源管理器模板可以轻松地通过单个协调操作创建资源。 模板是一个 JSON 文档，定义部署所需的资源。 它还可以指定部署参数。 使用模板时，参数用于提供输入值。

有关详细信息，请参阅[使用 Azure 资源管理器模板部署应用程序](../../azure-resource-manager/resource-group-template-deploy.md)。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* 若要在 CLI 中使用模板，需要安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>资源管理器模板

以下资源管理器模板可用于创建 Azure 机器学习服务工作区和关联的 Azure 资源:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

此模板创建以下 Azure 服务：

* Azure 资源组
* Azure 存储空间帐户
* Azure Key Vault
* Azure Application Insights
* Azure 容器注册表
* Azure 机器学习工作区

资源组是保存服务的容器。 Azure 机器学习工作区需要多种服务。

示例模板包含两个参数：

* **位置**：将在其中创建资源组和服务。

    模板将使用你为大多数资源选择的位置。 例外的情况是 Application Insights 服务，它不像其他所有服务一样在所有位置都可用。 如果选择了 Application Insights 服务不可用的位置，将在美国中南部位置创建该服务。

* **工作区名称**：Azure 机器学习工作区的友好名称。

    其他服务的名称将随机生成。

有关模板的详细信息，请参阅以下文章：

* [创作 Azure 资源管理器模板](../../azure-resource-manager/resource-group-authoring-templates.md)
* [使用 Azure 资源管理器模板部署应用程序](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices 资源类型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 遵循[从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)中的步骤。 显示“编辑模板”屏幕后，请粘贴本文档中所述的模板。
1. 选择“保存”以使用该模板。 提供以下信息并同意列出的条款和条件：

   * 订阅：选择用于这些资源的 Azure 订阅。
   * 资源组：选择或创建一个用于包含服务的资源组。
   * 工作区名称：要创建的 Azure 机器学习工作区所用的名称。 工作区名称的长度必须为 3 到 33 个字符。 只能包含字母数字字符和“-”。
   * 位置：选择要在其中创建资源的位置。

有关详细信息，请参阅[从自定义模板部署资源](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

此示例假设已将模板保存到当前目录中名为 `azuredeploy.json` 的文件：

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)和[使用 SAS 令牌和 Azure PowerShell 部署专用资源管理器模板](../../azure-resource-manager/resource-manager-powershell-sas-token.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

此示例假设已将模板保存到当前目录中名为 `azuredeploy.json` 的文件：

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)和[使用 SAS 令牌和 Azure CLI 部署专用资源管理器模板](../../azure-resource-manager/resource-manager-cli-sas-token.md)。

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 访问策略和 Azure 资源管理器模板

使用 Azure 资源管理器模板创建工作区和关联资源 (包括 Azure Key Vault) 时多次。 例如, 将模板多次用于与持续集成和部署管道的一部分的相同参数。

大多数通过模板创建的资源操作都是幂等的, 但 Key Vault 每次使用模板时都将清除访问策略。 清除访问策略会断开使用该访问策略的任何现有工作区对 Key Vault 的访问。 例如, Azure Notebooks VM 的停止/创建功能可能会失败。  

若要避免此问题, 建议采用以下方法之一:

*  不要对相同的参数多次部署模板。 或删除现有资源, 然后使用模板重新创建它们。
  
* 检查 Key Vault 访问策略, 然后使用这些策略设置模板的 accessPolicies 属性。
* 检查 Key Vault 资源是否已存在。 如果是这样, 请不要通过模板重新创建它。 例如, 添加一个参数, 该参数允许你禁用 Key Vault 资源的创建 (如果已存在)。

## <a name="next-steps"></a>后续步骤

* [使用资源管理器模板和资源管理器 REST API 部署资源](../../azure-resource-manager/resource-group-template-deploy-rest.md)。
* [通过 Visual Studio 创建和部署 Azure 资源组](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。
