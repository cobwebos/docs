---
title: 在 SQL 托管实例上手动启动故障转移
description: 了解如何在 Azure SQL 托管实例上手动故障转移主要副本和次要副本。
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 08/12/2020
ms.openlocfilehash: faef8c29b2a5e75745e36c5d826b4feee2d60a98
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88169114"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>SQL 托管实例上用户启动的手动故障转移

本文介绍如何将 SQL 托管实例常规用途上的主节点手动故障转移 (GP) 和业务关键 (BC) 服务层，以及如何在 BC 服务层上手动故障转移辅助只读副本节点。

## <a name="when-to-use-manual-failover"></a>何时使用手动故障转移

[高可用性](../database/high-availability-sla.md)是 SQL 托管实例平台的基础部分，可以透明地用于数据库应用程序。 当节点性能下降或检测到故障时，或在每月定期软件更新中，在 Azure 中使用 SQL 托管实例的所有应用程序都需要进行从主节点到辅助节点的故障转移。

出于以下某些原因，你可能需要考虑在 SQL 托管实例上执行[手动故障转移](../database/high-availability-sla.md#testing-application-fault-resiliency)：
- 在部署到生产环境之前，测试应用程序的故障转移复原
- 测试用于自动故障转移的端到端系统故障恢复
- 测试故障转移如何影响现有数据库会话
- 验证故障转移是否更改了端到端性能，因为网络延迟发生变化
- 在某些情况下，查询性能下降，手动故障转移可帮助减轻性能问题。

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>在 SQL 托管实例上启动手动故障转移

### <a name="using-powershell"></a>使用 PowerShell

Az 的最低版本需要是[v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)。 请考虑使用始终具有最新 PowerShell 版本可用的 Azure 门户中的[Azure Cloud Shell](../../cloud-shell/overview.md) 。 

作为预先要求，请使用以下 PowerShell 脚本来安装所需的 Azure 模块。 此外，请选择要进行故障转移托管实例的订阅。

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

使用 PowerShell 命令[AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover)和以下示例启动主节点的故障转移，该节点适用于 BC 和 GP 服务层。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

使用以下 PS 命令故障转移只读辅助节点（仅适用于 BC 服务层）。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>使用 CLI

确保安装了最新的 CLI 脚本。

在以下示例中使用 az sql mi 故障转移 CLI 命令，启动主节点的故障转移（适用于 BC 和 GP 服务层）。

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

使用以下 CLI 命令来故障转移只读辅助节点（仅适用于 BC 服务层）。

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>使用 REST API

对于可能需要自动故障转移其 SQL 托管实例以实现持续测试管道或自动性能 mitigators 的高级用户，可以通过 API 调用启动故障转移来实现此功能。 有关详细信息，请参阅[托管实例-故障转移 REST API](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover) 。

要使用 REST API 调用启动故障转移，请首先使用所选的 API 客户端生成身份验证令牌。 生成的身份验证令牌用作 API 请求标头中的授权属性，它是必需的。

下面的代码是要调用的 API URI 的示例：

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

需要在 API 调用中传递以下属性：

| **API 属性** | **参数** |
| --- | --- |
| subscriptionId | 托管实例所部署到的订阅 ID |
| resourceGroupName | 包含托管实例的资源组 |
| managedInstanceName | 托管实例的名称 |
| replicaType |  (可选)  (Primary 或 ReadableSecondary) 。 这些参数表示要进行故障转移的副本类型：主要或可读的辅助副本。 如果未指定，则默认情况下，将在主副本上启动故障转移。 |
| api-version | 静态值，当前需要为 "2019-06-01-preview" |

API 响应将为以下两项之一：

- 202 已接受
- 400请求错误之一。

可以通过在响应标头中查看 API 响应来跟踪操作状态。 有关详细信息，请参阅[异步 Azure 操作的状态](../../azure-resource-manager/management/async-operations.md)。

## <a name="monitor-the-failover"></a>监视故障转移

若要监视用户启动的手动故障转移的进度，请在喜欢的客户端中执行以下 T-sql 查询 (例如，在 SQL 托管实例上运行 SSMS) 。 它将读取系统视图 sys. dm_hadr_fabric_replica_states，并报告实例上可用的副本。 启动手动故障转移后刷新相同的查询。

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

在启动故障转移之前，你的输出将指示 BC 服务层上的当前主副本，其中包含 AlwaysOn 可用性组中的一个主副本和三个辅助副本。 执行故障转移时，再次运行此查询需要指示主节点的更改。

对于 BC，你将不能看到与下面显示的 GP 服务层相同的输出。 这是因为 GP 服务层只基于单个节点。 对于 GP 服务层，t-sql 查询输出将只在故障转移前后显示单个节点。 在故障转移过程中，客户端在故障转移期间断开连接（通常持续时间很长）会指示故障转移的执行。

> [!IMPORTANT]
> 用户启动的手动故障转移的功能限制如下：
> - 在同一托管实例上，可能每隔30分钟启动一次 (1) 故障转移。
> - 对于 BC 实例，要接受故障转移请求，必须存在仲裁副本。
> - 对于 BC 实例，不能指定要在其上启动故障转移的可读辅助副本。

## <a name="next-steps"></a>后续步骤

- 详细了解[AZURE SQL 托管实例](../database/high-availability-sla.md)的托管实例高可用性的高可用性。
- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
