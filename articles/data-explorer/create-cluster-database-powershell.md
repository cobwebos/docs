---
title: 快速入门：使用 PowerShell 创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 PowerShell 创建 Azure 数据资源管理器群集和数据库
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 28785bb99dcdb767a64ae977e8326b80130fb135
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240192"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>使用 PowerShell 创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 在本快速入门中，你将使用 Powershell 创建群集和数据库。 可以在 Windows、Linux 或 [Azure Cloud Shell](../cloud-shell/overview.md) 中使用 [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) 运行 PowerShell cmdlet 和脚本，以创建和配置 Azure 数据资源管理器群集和数据库。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 Azure CLI，本快速入门要求 Azure CLI 2.0.4 或更高版本。 请运行 `az --version` 检查版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="configure-parameters"></a>配置参数

如果在 Azure Cloud Shell 中运行命令，则不需要执行以下步骤。 如果在本地运行 CLI，请按步骤 1 和 2 登录到 Azure 并设置当前订阅：

1. 运行以下命令来登录到 Azure：

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 设置要在其中创建群集的订阅：

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. 在本地或 Azure Cloud Shell 中运行 Azure CLI 时，需要在设备上安装 Az.Kusto 模块：
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>创建 Azure 数据资源管理器群集

1. 请使用以下命令创建群集：

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | 名称 | mykustocluster  | 所需的群集名称。|
   | SKU | *D13_v2* | 将用于群集的 SKU。 |
   | ResourceGroupName | *testrg* | 将在其中创建群集的资源组名称。 |

    可以使用其他可选参数，例如群集的容量。

1. 运行以下命令，检查群集是否已成功创建：

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

如果结果包含带 `Succeeded` 值的 `provisioningState`，则表示已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 请使用以下命令创建数据库：

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | ClusterName | mykustocluster  | 将在其中创建数据库的群集的名称。|
   | 名称 | mykustodatabase  | 数据库名称。|
   | ResourceGroupName | *testrg* | 将在其中创建群集的资源组名称。 |
   | SoftDeletePeriod | *3650:00:00:00* | 供查询使用的数据的保留时间。 |
   | HotCachePeriod | *3650:00:00:00* | 数据将在缓存中保留的时间。 |

1. 若要查看已创建的数据库，请运行以下命令：

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果计划学习其他快速入门和教程，请保留创建的资源。
* 若要清理资源，请删除群集。 删除群集时，也会删除其中的所有数据库。 使用以下命令删除群集：

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>后续步骤

* [其他 Az.Kusto 命令](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [快速入门：使用 Azure 数据资源管理器 .NET Standard SDK（预览版）引入数据](net-standard-ingest-data.md)
