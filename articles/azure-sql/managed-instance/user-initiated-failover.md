---
title: 在 SQL 托管实例上手动启动故障转移
description: 了解如何在 Azure SQL 托管实例上手动对主要副本和次要副本进行故障转移。
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 08/31/2020
ms.openlocfilehash: 3be0695c20eafb71564211d1168bc59813f8800a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617751"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>SQL 托管实例上用户启动的手动故障转移

本文介绍如何在 SQL 托管实例常规用途 (GP) 和业务关键 (BC) 服务层级上手动对主节点进行故障转移，以及如何仅在 BC 服务层上对辅助只读副本节点进行手动故障转移。

## <a name="when-to-use-manual-failover"></a>何时使用手动故障转移

[高可用性](../database/high-availability-sla.md)是 SQL 托管实例平台的基本功能，其运作对数据库应用程序透明。 当节点性能下降或检测到故障时，或在每月定期软件更新期间，在 Azure 中使用 SQL 托管实例的所有应用程序都会发生从主节点到辅助节点的故障转移。

出于以下原因，你可能会考虑在 SQL 托管实例上执行[手动故障转移](../database/high-availability-sla.md#testing-application-fault-resiliency)：
- 在部署到生产环境之前，测试应用程序的故障转移复原能力
- 测试端到端系统在自动故障转移时的错误复原能力
- 测试故障转移如何影响现有数据库会话
- 验证故障转移是否由于网络延迟的变化而更改了端到端性能
- 在某些情况下，查询性能降低，手动故障转移可帮助减轻性能问题。

> [!NOTE]
> 在部署到生产环境之前，请确保应用程序的故障转移是可复原的，这有助于降低生产环境中出现应用程序故障的风险，且有助于向客户提供应用程序可用性。

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>在 SQL 托管实例上启动手动故障转移

### <a name="rbac-permissions-required"></a>需要 RBAC 权限

启动故障转移的用户需要具有下列 RBAC 角色之一：

- 订阅所有者角色或
- 托管实例参与者角色，或
- 具有以下权限的自定义角色：
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>使用 PowerShell

Az.Sql 的最低版本须为 [v2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)。 请考虑使用始终具有最新 PowerShell 版本可用的 Azure 门户中的 [Azure Cloud Shell](../../cloud-shell/overview.md) 。 

作为一项先决条件，请使用以下 PowerShell 脚本来安装所需的 Azure 模块。 此外，请选择要进行故障转移的托管实例所在的订阅。

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

将 PowerShell 命令 [Invoke-AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover) 与以下示例结合使用以启动主节点的故障转移（适用于 BC 和 GP 服务层）。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

使用以下 PS 命令来对只读辅助节点进行故障转移（仅适用于 BC 服务层）。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>使用 CLI

请确保安装了最新的 CLI 脚本。

将 az sql mi failover CLI 命令用于以下示例，以启动主节点的故障转移（适用于 BC 和 GP 服务层）。

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

使用以下 CLI 命令来对只读辅助节点进行故障转移（仅适用于 BC 服务层）。

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>使用 REST API

对于可能需要对其 SQL 托管实例进行自动故障转移以实现持续测试管道的高级用户，或是自动性能缓冲器，可以通过使用 API 调用启动故障转移来实现此功能。 有关详细信息，请参阅[托管实例 - 故障转移 REST API](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover)。

若要使用 REST API 调用启动故障转移，请首先使用所选的 API 客户端生成身份验证令牌。 生成的身份验证令牌将用作 API 请求标头中的授权属性，它是必需的。

以下代码是要调用的 API URI 的示例：

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

需要在 API 调用中传递以下属性：

| **API 属性** | **参数** |
| --- | --- |
| subscriptionId | 托管实例所部署到的订阅 ID |
| resourceGroupName | 包含托管实例的资源组 |
| managedInstanceName | 托管实例的名称 |
| replicaType | （可选）（Primary 或 ReadableSecondary）。 这些参数表示要进行故障转移的副本类型：主要副本或可读辅助副本。 如果未指定，则默认情况下，将在主副本上启动故障转移。 |
| api-version | 静态值，当前需要为“2019-06-01-preview” |

API 响应将为以下两项之一：

- 202 已接受
- 400 请求错误之一。

可通过在响应标头中查看 API 响应来跟踪操作状态。 有关详细信息，请参阅[异步 Azure 操作状态](../../azure-resource-manager/management/async-operations.md)。

## <a name="monitor-the-failover"></a>监视故障转移

若要监视用户启动的手动故障转移的进度，请在你喜欢的客户端（例如 SSMS）中对 SQL 托管实例执行以下 T-SQL 查询。 它将读取系统视图 sys.dm_hadr_fabric_replica_states 并报告实例上可用的副本。 启动手动故障转移后刷新相同的查询。

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

启动故障转移之前，你的输出将指示 BC 服务层上的当前主副本，其中包含 AlwaysOn 可用性组中的一个主副本和三个次要副本。 执行故障转移后，再次运行此查询将需要指示主节点的更改。

在 GP 服务层中，你将无法看到与上面所示的 BC 的输出相同的输出。 这是因为 GP 服务层只基于单个节点。 GP 服务层的 T-SQL 查询输出将仅在故障转移前后显示单个节点。 在故障转移过程中，客户端丢失的连接（通常持续不到一分钟）将作为故障转移执行的指示。

> [!NOTE]
> 在高强度工作负载的情况下，完成故障转移过程（不是实际的短暂不可用性）可能需要几分钟的时间。 这是因为实例引擎在能够进行故障转移之前正在处理主节点上的所有当前事务，并跟进辅助节点。

> [!IMPORTANT]
> 用户启动的手动故障转移的功能限制如下：
> - 每 30 分钟在同一托管实例上可能会启动一次 (1) 故障转移。
> - 对于 BC 实例，若要使故障转移请求得到接受，必须存在仲裁副本。
> - 对于 BC 实例，不能指定要在其上启动故障转移的可读辅助副本。

## <a name="next-steps"></a>后续步骤

- 详细了解托管实例的高可用性 [Azure SQL 托管实例的高可用性](../database/high-availability-sla.md)。
- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
