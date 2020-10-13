---
title: 取消管理操作
titleSuffix: Azure SQL Managed Instance
description: 了解如何取消 Azure SQL 托管实例管理操作。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 4ec999cc35e7d18287679c74c6d45a5aa2ecb9e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994652"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>正在取消 Azure SQL 托管实例管理操作
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例提供取消某些 [管理操作](management-operations-overview.md)（例如，在部署新的托管实例或更新实例属性时）的功能。 

## <a name="overview"></a>概述

 所有管理操作可分类为：

- 部署实例（创建新实例）。
- 实例更新（更改实例属性，例如 vCore 数或预留存储）。
- 删除实例。

可以 [监视管理操作的进度和状态](management-operations-monitor.md) ，并根据需要取消其中一些操作。 

下表汇总了管理操作，无论你是否可以取消这些操作，并汇总其典型的总持续时间：

类别  |操作  |可取消  |估计取消持续时间  |
|---------|---------|---------|---------|
|部署 |实例创建 |是 |90% 的操作可在 5 分钟内完成。 |
|更新 |实例存储纵向缩放（“常规用途”服务层级） |否 |  |
|更新 |实例存储纵向缩放（“业务关键”服务层级） |是 |90% 的操作可在 5 分钟内完成。 |
|更新 |实例计算 (vCore) 纵向缩放（“常规用途”） |是 |90% 的操作可在 5 分钟内完成。 |
|更新 |实例计算 (vCore) 纵向缩放（“业务关键”） |是 |90% 的操作可在 5 分钟内完成。 |
|更新 |实例服务层级更改（从“常规用途”更改为“业务关键”，或反之） |是 |90% 的操作可在 5 分钟内完成。 |
|Delete |实例删除 |否 |  |
|删除 |虚拟群集的删除（用户启动的操作） |否 |  |

## <a name="cancel-management-operation"></a>取消管理操作

# <a name="portal"></a>[门户](#tab/azure-portal)

若要使用 Azure 门户取消管理操作，请执行以下步骤：

1. 中转到 [Azure 门户](https://portal.azure.com)
1. 请参阅 SQL 托管实例的 " **概述** " 边栏选项卡。 
1. 选择正在进行的操作旁边的 **通知** 框，打开 **正在进行的操作** 页。 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="选择 &quot;正在进行的操作&quot; 框，打开正在进行的操作页。&quot;:::

1. 选择页面底部的 **&quot;取消" 操作** 。 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="选择 &quot;正在进行的操作&quot; 框，打开正在进行的操作页。&quot;:::

1. 选择页面底部的 **&quot;取消" 按钮处于非活动状态，则表示管理操作已进入不可取消的状态，这将很快完成。  管理操作将继续执行，直到完成。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果尚未安装 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要取消管理操作，需要指定管理操作名称。 因此，请首先使用 get 命令检索操作列表，然后取消特定操作。

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

有关详细的命令说明，请参阅 [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) 和 [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要取消管理操作，需要指定管理操作名称。 因此，请首先使用 get 命令检索操作列表，然后取消特定操作。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

有关详细的命令说明，请参阅 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op)。

---

## <a name="canceled-deployment-request"></a>已取消部署请求

对于 API 版本2020-02-02，只要接受实例创建请求，实例就会开始作为资源存在，无论部署过程的进度 **如何)  (** 托管实例状态。 如果取消实例部署请求)  (创建新实例，则托管实例将从 **预配** 状态切换到 **FailedToCreate**。

无法创建的实例仍以资源的形式存在： 

- 不收取费用
- 不要计算 (子网或 vCore 配额的资源限制) 
- 保持实例名称为 "已保留"-若要部署具有相同名称的实例，请删除失败的实例以释放名称


> [!NOTE]
> 若要最大程度地减少资源或托管实例的干扰，请删除无法部署的实例或已取消部署的实例。 


## <a name="next-steps"></a>后续步骤

- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
- 有关功能和比较列表，请参阅[常用 SQL 功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅 [SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
