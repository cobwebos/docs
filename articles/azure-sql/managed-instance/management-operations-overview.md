---
title: 管理操作
titleSuffix: Azure SQL Managed Instance
description: 了解 Azure SQL 托管实例管理操作持续时间和最佳实践。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531039"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Azure SQL 托管实例管理操作概述
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>什么是管理操作？
Azure SQL 托管实例提供管理操作，你可以使用这些操作来自动部署新的托管实例，更新实例属性，以及在不再需要时删除实例。

为了支持[Azure 虚拟网络中的部署](../../virtual-network/virtual-network-for-azure-services.md)并为客户提供隔离和安全性，SQL 托管实例依赖于[虚拟群集](connectivity-architecture-overview.md#high-level-connectivity-architecture)。 虚拟群集表示在客户的虚拟网络子网中部署的一组专用虚拟机。 实质上，每在一个空子网中部署一个托管实例，就会组建一个新的虚拟群集。

对已部署的托管实例的后续操作也可能对基础虚拟群集产生影响。 这些操作会影响管理操作的持续时间，因为部署更多的虚拟机所需的开销需要在计划新部署或对现有托管实例的更新时考虑。

所有管理操作可分类为：

- 实例部署（新实例的创建）。
- 实例更新（更改实例属性，如 Vcore 或保留存储）。
- 实例删除。

## <a name="management-operations-duration"></a>管理操作持续时间
通常，在虚拟群集上执行的操作花费的时间最长。 虚拟群集上的操作持续时间可变–下面是根据现有的服务遥测数据，通常可以预期的值：

- **虚拟群集创建**：创建是实例管理操作中的一个同步步骤。 **90% 的操作可在 4 小时内完成**。
- **虚拟群集大小调整（扩展或收缩）**：扩展是一种同步步骤，而收缩是异步执行的（不影响实例管理操作的持续时间）。 **90% 的群集扩展操作可在 2.5 小时内完成**。
- **虚拟群集删除**：删除是一个异步步骤，但也可以在空虚拟群集上[手动启动](virtual-cluster-delete.md)，在这种情况下，它会同步执行。 **90% 的虚拟群集删除操作可在 1.5 小时内完成**。

此外，实例的管理还可能包括对托管数据库执行的一项操作，这些操作将导致更长的持续时间：

- **附加 Azure 存储中的数据库文件**：同步步骤（如计算（vcore）），或在常规用途服务层中增加或减少存储。 **90% 的此类操作可在 5 分钟内完成**。
- **Always On 可用性组种子设定**：同步步骤，如计算（vcore）或业务关键服务层中的存储缩放，以及将服务层从常规用途更改为业务关键（反之亦然）。 此操作的持续时间与总数据库大小以及当前数据库活动（活动的事务数）成正比。 更新实例时执行数据库活动可能会使总持续时间发生明显的变化。 **90% 的这些操作的执行频率为 220 GB/小时或更高**。

下表汇总了操作及其典型的总持续时间：

|类别  |操作  |长时间运行的分段  |预计持续时间  |
|---------|---------|---------|---------|
|**部署** |空子网中的第一个实例|虚拟群集的创建|90% 的操作在4小时内完成。|
|部署 |非空子网中另一个硬件代系的第一个实例（例如，包含第 4 代实例的子网中的第一个 5 代实例）|虚拟群集的创建*|90% 的操作在4小时内完成。|
|部署 |在空子网或非空子网中创建4个 Vcore 的第一个实例|虚拟群集的创建**|90% 的操作在4小时内完成。|
|部署 |在非空子网中创建后续实例（第 2 个、第 3 个 ... 实例）|虚拟群集大小调整|90% 的操作在2.5 小时内完成。|
|**Update** |实例属性更改（管理员密码、Azure AD 登录名、Azure 混合权益标志）|不可用|最多1分钟。|
|更新 |实例存储纵向缩放（“常规用途”服务层级）|附加数据库文件|90% 的操作在5分钟内完成。|
|更新 |实例存储纵向缩放（“业务关键”服务层级）|- 虚拟群集大小调整<br>- Always On 可用性组种子设定|90% 的操作将在2.5 小时内完成，并对所有数据库（220 GB/小时）进行种子设定。|
|更新 |实例计算 (vCore) 纵向缩放（“常规用途”）|- 虚拟群集大小调整<br>- 附加数据库文件|90% 的操作在2.5 小时内完成。|
|更新 |实例计算 (vCore) 纵向缩放（“业务关键”）|- 虚拟群集大小调整<br>- Always On 可用性组种子设定|90% 的操作将在2.5 小时内完成，并对所有数据库（220 GB/小时）进行种子设定。|
|更新 |实例服务层级更改（从“常规用途”更改为“业务关键”，或反之）|- 虚拟群集大小调整<br>- Always On 可用性组种子设定|90% 的操作将在2.5 小时内完成，并对所有数据库（220 GB/小时）进行种子设定。|
|**删除**|实例删除|所有数据库的尾部日志备份|90% 的操作在最多 1 分钟内即可完成。<br>注意：如果删除了子网中的最后一个实例，此操作将在12小时后计划删除虚拟群集。 * *|
|删除|虚拟群集的删除（用户启动的操作）|虚拟群集的删除|90% 的操作最多可完成1.5 小时。|

\* 虚拟群集是按硬件代系构建的。

\*\* 12 小时是当前配置，但将来可能会更改，因此，请不要过度依赖于此配置。 如果你之前需要删除虚拟群集（例如，要释放子网），请参阅删除[托管实例后删除子网](virtual-cluster-delete.md)。

## <a name="instance-availability-during-management-operations"></a>管理操作期间的实例可用性

SQL 托管实例**在更新操作期间可用**，但在更新结束时发生的故障转移导致的短暂停机时间除外。 即使经过[加速的数据库恢复](../accelerated-database-recovery.md)，它通常还会持续10秒钟，即使中断长时间运行的事务也是如此。

> [!IMPORTANT]
> 不建议缩放 Azure SQL 托管实例的计算或存储，也不建议同时使用长时间运行的事务（数据导入、数据处理作业、索引重新生成等）更改服务层。 在操作结束时执行的数据库故障转移将取消所有正在进行的事务。

在部署和删除操作过程中，SQL 托管实例不能用于客户端应用程序。

## <a name="management-operations-cross-impact"></a>管理操作交叉影响

托管实例上的管理操作可能会影响置于同一虚拟群集内的实例的其他管理操作：

- 虚拟群集中**长时间运行的还原操作**会将其他实例创建或缩放操作置于同一子网中。<br/>**示例：** 如果有长时间运行的还原操作，并且在同一子网中存在创建或缩放请求，则完成此请求将需要更长时间，因为它将等待还原操作完成，然后再继续。
    
- **后续实例创建或缩放**操作由先前启动的实例创建或启动虚拟群集大小的实例缩放置于保持状态。<br/>**示例：** 如果在同一虚拟群集下的同一子网中存在多个创建和/或缩放请求，并且其中一个请求启动了虚拟群集大小调整，则在需要虚拟群集大小的情况下，在该虚拟群集大小超过5分钟后提交的所有请求都将持续比预期更长的时间。

- **在5分钟的时间段内提交的创建/缩放操作**将以并行方式进行批处理和执行。<br/>**示例：** 对于在5分钟的时间段内提交的所有操作（从执行第一个操作请求那一刻开始测量），只会执行一次虚拟群集大小调整。 如果提交第一个请求后超过5分钟提交另一个请求，则会在执行开始之前等待虚拟群集大小调整完成。

> [!IMPORTANT]
> 满足继续执行的条件后，因另一个操作正在进行而被暂停的管理操作将自动恢复运行。 不需要用户执行任何操作即可临时恢复暂停的管理操作。

## <a name="canceling-management-operations"></a>取消管理操作

下表总结了取消特定管理操作和典型总持续时间的功能：

类别  |操作  |可取消  |估计取消持续时间  |
|---------|---------|---------|---------|
|部署 |实例创建 |否 |  |
|更新 |实例存储纵向缩放（“常规用途”服务层级） |否 |  |
|更新 |实例存储纵向缩放（“业务关键”服务层级） |是 |90% 的操作在5分钟内完成。 |
|更新 |实例计算 (vCore) 纵向缩放（“常规用途”） |是 |90% 的操作在5分钟内完成。 |
|更新 |实例计算 (vCore) 纵向缩放（“业务关键”） |是 |90% 的操作在5分钟内完成。 |
|更新 |实例服务层级更改（从“常规用途”更改为“业务关键”，或反之） |是 |90% 的操作在5分钟内完成。 |
|删除 |实例删除 |否 |  |
|删除 |虚拟群集的删除（用户启动的操作） |否 |  |

# <a name="portal"></a>[门户](#tab/azure-portal)

要取消管理操作，请切换到 "概述" 边栏选项卡，并单击正在进行的操作的通知框。 在右侧，将出现带有正在进行的操作的屏幕，并且将显示 "取消" 操作按钮。 第一次单击之后，系统会要求您再次单击并确认您要取消该操作。

[![取消操作](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

提交并处理取消请求后，如果取消提交成功，则会收到通知。

如果提交的取消请求成功，则会在几分钟内取消管理操作，从而导致失败。

![正在取消操作结果](./media/management-operations-overview/canceling-operation-result.png)

如果取消请求失败或 "取消" 按钮处于非活动状态，则表示管理操作已进入不可取消的状态，并将在几分钟内完成。 管理操作将继续执行，直到完成。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果尚未安装 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要取消管理操作，需要指定管理操作名称。 因此，首先使用 get 命令检索操作列表，然后取消特定操作。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

有关详细的命令说明，请参阅[AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)和[AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要取消管理操作，需要指定管理操作名称。 因此，首先使用 get 命令检索操作列表，然后取消特定操作。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

有关详细的命令解释，请参阅[az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest)。

---

## <a name="next-steps"></a>后续步骤
- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅[SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
