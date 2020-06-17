---
title: 快速入门 - 使用 PowerShell 创建 Azure Databricks 工作区
description: 此快速入门介绍如何使用 PowerShell 创建 Azure Databricks 工作区。
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485684"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>快速入门：使用 PowerShell 创建 Azure Databricks 工作区

此快速入门介绍如何使用 PowerShell 创建 Azure Databricks 工作区。 可以使用 PowerShell 以交互方式或者通过脚本创建和管理 Azure 资源。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 尽管 Az.Databricks PowerShell 模块为预览版，但必须使用以下命令从 Az PowerShell 模块单独安装它：`Install-Module -Name Az.Databricks -AllowPrerelease`。
> Az.Databricks PowerShell 模块正式版推出后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中原生提供。

如果这是你第一次使用 Azure Databricks，则必须注册 **Microsoft.Databricks** 资源提供程序。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定的订阅 ID。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

以下示例在“美国西部 2”区域创建名为“myresourcegroup”的资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

在本部分，使用 PowerShell 创建 Azure Databricks 工作区。

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

提供以下值：

|       **属性**       |                                                                                **说明**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 名称                     | 提供 Databricks 工作区的名称                                                                                                                                   |
| ResourceGroupName        | 指定现有资源组名称                                                                                                                                        |
| 位置                 | 选择“美国西部 2”。 有关其他可用区域，请参阅[各区域推出的 Azure 服务](https://azure.microsoft.com/regions/services/)                                     |
| ManagedResourceGroupName | 指定是要创建新的受管理资源组还是使用现有受管理资源组。                                                                                        |
| SKU                      | 在“标准”、“高级”和“试用”之间进行选择。   有关这些层的详细信息，请参阅 [Databricks 定价](https://azure.microsoft.com/pricing/details/databricks/) |

创建工作区需要几分钟时间。 完成此过程后，你的用户帐户将自动添加为工作区的管理员用户。

当工作区部署失败时，仍然会在失败状态下创建工作区。 删除失败的工作区，并创建一个解决部署错误的新工作区。 删除失败的工作区时，托管资源组和任何成功部署的资源也将被删除。

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>确定 Databricks 工作区的预配状态

若要确定 Databricks 工作区是否已成功预配，可以使用 `Get-AzDatabricksWorkspace` cmdlet。

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>清理资源

如果其他快速入门或教程不需要使用本快速入门中创建的资源，可以运行以下示例将其删除。

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本快速入门范围外的资源，这些资源也会被删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

若要仅删除本快速入门中创建的服务器而不删除资源组，请使用 `Remove-AzDatabricksWorkspace` cmdlet。

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Databricks 中创建 Spark 群集](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
