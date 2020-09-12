---
title: 无服务器计算层
description: 本文介绍新的无服务器计算层，并将它与现有的 Azure SQL 数据库预配计算层进行比较。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 9/8/2020
ms.openlocfilehash: 979976ba88c2acca282a7f8bef4784b9d91ce0aa
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565083"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL 数据库无服务器
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

无服务器是 Azure SQL 数据库中单一数据库的计算层，可根据工作负载需求自动缩放计算，并按每秒使用的计算量计费。 此外，当仅对存储计费时，无服务器计算层将在非活动期间自动暂停数据库；当活动返回时，它将自动恢复数据库。

## <a name="serverless-compute-tier"></a>无服务器计算层

Azure SQL 数据库中单一数据库的无服务器计算层由计算自动缩放范围和 autopause 延迟参数化。 这些参数的配置将组成数据库性能经验和计算成本。

![无服务器计费](./media/serverless-tier-overview/serverless-billing.png)

### <a name="performance-configuration"></a>性能配置

- “最小 vCore 数”和“最大 vCore 数”是可配置的参数，用于定义数据库可用的计算容量范围。  内存和 IO 限制与指定的 vCore 范围成正比。  
- **自动暂停延迟**是可配置的参数，用于定义数据库在自动暂停之前必须处于非活动状态的时间段。 发生下次登录或其他活动时，数据库会自动恢复。  或者，可以禁用自动暂停。

### <a name="cost"></a>成本

- 无服务器数据库的成本是计算成本和存储成本之和。
- 如果计算用量介于配置的下限和上限之间，则计算成本基于使用的 vCore 数和内存量。
- 如果计算用量低于配置的下限，则计算成本基于配置的最小 vCore 数和最小内存量。
- 暂停数据库后，计算成本将会归零，只会产生存储成本。
- 存储成本的确定方式与在预配计算层中相同。

有关成本的更多详细信息，请参阅[计费](serverless-tier-overview.md#billing)。

## <a name="scenarios"></a>方案

对于具有间歇性、不可预测的使用模式，在空闲使用时间段后的计算预热期间能够容忍一定延迟的单一数据库来说，无服务器是一种高性价比的方案。 相比之下，对于具有较高的平均使用量，并且计算预热期间无法容忍任何延迟的单一数据库或多个数据库，预配计算层是更具性价比的方案。

### <a name="scenarios-well-suited-for-serverless-compute"></a>适合无服务器计算的场景

- 具有间歇性、不可预测的使用模式，中间穿插着非活动时段且一段时间内平均计算利用率较低的单一数据库。
- 预配计算层中经常重新缩放的单一数据库；客户更倾向于将计算重新缩放委托到服务。
- 不提供用量历史记录，且在 SQL 数据库中部署之前难以或者无法估算计算大小的新单一数据库。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>适合预配计算的场景

- 采用较有规律的可预测使用模式，且在一段时间内平均计算利用率较高的单一数据库。
- 不能容忍因较频繁的内存微调或从暂停状态下自动恢复的延迟，而导致需要对性能做出妥协的数据库。
- 采用间歇性、不可预测使用模式，可整合到弹性池以提高性价比优化的多个数据库。

## <a name="comparison-with-provisioned-compute-tier"></a>与预配计算层的比较

下表汇总了无服务器计算层和预配计算层之间的区别：

| | **无服务器计算** | **预配计算** |
|:---|:---|:---|
|**数据库使用模式**| 间歇性、不可预测的使用模式，在一段时间内的平均计算利用率较低。 | 较有规律的使用模式，在一段时间内平均计算利用率较高；或者使用弹性池的多个数据库。|
| **性能管理工作量** |较低|较高|
|**计算缩放**|自动|手动|
|**计算响应能力**|在非活动期后较低|即时|
|**计费粒度**|每秒|每小时|

## <a name="purchasing-model-and-service-tier"></a>购买模型和服务层级

目前，仅 vCore 购买模型中第 5 代硬件上的常规用途层中支持 SQL 数据库无服务器。

## <a name="autoscaling"></a>自动缩放

### <a name="scaling-responsiveness"></a>缩放响应能力

通常，无服务器数据库在具有足够运算能力的计算机上运行，以满足资源需求，而不会中断由最大 vCore 数值设置的限制范围内任何数量的计算请求。 有时，如果计算机无法在几分钟内满足资源需求，系统便会自动进行负载均衡。 例如，如果资源需求为 4 个 vCore，但只有 2 个 vCore 可用，则在提供 4 个 vCore 之前，可能需要几分钟时间进行负载均衡。 在负载均衡过程中，除了在连接丢失，操作结束时的一小段时间内，数据库将保持联机状态。

### <a name="memory-management"></a>内存管理

无服务器数据库的内存回收比预配的计算数据库的内存回收更加频繁。 这种行为对于在无服务器条件下控制成本非常重要，并可能会影响性能。

#### <a name="cache-reclamation"></a>缓存回收

与预配的计算数据库不同，当 CPU 或活动缓存使用率较低时，从无服务器数据库回收 SQL 缓存中的内存。

- 当最近使用的缓存条目的总大小低于某个时间段的阈值时，主动缓存利用率将视为低。
- 触发缓存回收后，目标缓存大小将递减到以前大小的一部分，并且仅当使用率保持较低时才继续回收。
- 发生缓存回收时，用于选择要逐出的缓存条目的策略与当内存压力较高时适用于预配计算数据库的策略相同。
- 缓存大小永远不会减至小于最小 vCore 数定义的最小内存限制（可配置）。

在无服务器数据库和预配的计算数据库中，如果使用了所有可用内存，则可能会逐出缓存条目。

请注意，当 CPU 使用率较低时，主动缓存利用率可能会保持较高水平（具体取决于使用模式），并会阻止内存回收。  此外，用户活动停止后，可能会有额外的延迟，因为响应先前用户活动会定期后台进程。  例如，"删除" 操作将生成标记为删除的虚影记录，但不会在物理上删除，直到虚影清除进程运行，这可能涉及到将数据页读入缓存。

#### <a name="cache-hydration"></a>缓存合成

随着从磁盘中不断提取数据，SQL 缓存也会不断增大，其增长速度与预配的数据库相同。 当数据库处于繁忙状态时，允许缓存无约束增大，但不能超过最大内存限制。

## <a name="autopausing-and-autoresuming"></a>自动暂停和自动恢复

### <a name="autopausing"></a>自动暂停

如果在自动暂停延迟的时间段内，下面的所有条件均成立，则会触发自动暂停：

- 会话数目 = 0
- CPU = 0（对于在用户池中运行的用户工作负荷）

如有需要，系统也提供了禁用自动暂停的选项。

以下功能不支持自动暂停，但支持自动缩放。  如果使用了下列任一功能，则应禁用 autopausing 并且数据库将保持联机状态，而不考虑数据库的非活动持续时间：

- 异地复制（活动异地复制和自动故障转移组）。
- 长期备份保留 (LTR)。
- SQL 数据同步中使用的同步数据库。与同步数据库不同，中心数据库和成员数据库支持自动暂停。
- DNS 别名
- 弹性作业中使用的作业数据库 (预览) 。

在部署某些需要数据库联机的服务更新期间，会暂时阻止自动暂停。  在这种情况下，一旦服务更新完成，就会再次允许自动暂停。

### <a name="autoresuming"></a>自动恢复

如果在任何时候，下面的任意条件成立，均会触发自动恢复：

|功能|自动恢复触发器|
|---|---|
|身份验证和授权|登录|
|威胁检测|启用/禁用数据库或服务器级别的威胁检测设置。<br>修改数据库或服务器级别的威胁检测设置。|
|数据发现和分类|添加、修改、删除或查看敏感度标签|
|审核|查看审核记录。<br>更新或查看审核策略。|
|数据屏蔽|添加、修改、删除或查看数据屏蔽规则|
|透明数据加密|查看透明数据加密的状况或状态|
|漏洞评估|如果启用，则为临时扫描和定期扫描|
|查询（性能）数据存储|修改或查看查询存储设置|
|自动优化|自动优化建议的应用和验证，例如自动索引|
|数据库复制|创建数据库作为副本。<br>导出到 BACPAC 文件。|
|SQL 数据同步|按照可配置的时间表或手动执行中心和成员数据库之间的同步|
|修改特定的数据库元数据|添加新的数据库标记。<br>更改最大 vCore 数、最小 vCore 数或自动暂停延迟。|
|SQL Server Management Studio (SSMS)|使用早于 18.1 的 SSMS 版本并为服务器中的任何数据库打开新的查询窗口，将恢复同一服务器中任何自动暂停的数据库。 如果使用 SSMS 版本 18.1 或更高版本，则不会发生此行为。|

监视解决方案、管理解决方案或其他执行上述任何操作的解决方案将触发自动恢复。

在部署某些需要数据库联机的服务更新时，也会触发自动刷新。

### <a name="connectivity"></a>连接

如果无服务器数据库处于暂停状态，则首次登录将恢复数据库，并返回一个错误，指出数据库将不可用，错误代码为 40613。 恢复数据库后，必须重新尝试登录以建立连接。 具有连接重试逻辑的数据库客户端应该不需要进行修改。

### <a name="latency"></a>延迟

自动恢复或自动暂停无服务器数据库的延迟时间通常为 1 分钟自动恢复，1-10 分钟自动暂停。

### <a name="customer-managed-transparent-data-encryption-byok"></a>客户托管透明数据加密 (BYOK)

如果使用[客户托管的透明数据加密](transparent-data-encryption-byok-overview.md) (BYOK)，并且在发生密钥删除或吊销时自动暂停了无服务器数据库，则该数据库将保持自动暂停状态。  在这种情况下，在下次恢复数据库后，大约 10 分钟内数据库将无法访问。  该数据库变为不可访问后，恢复过程将与预配的计算数据库相同。  如果在发生密钥删除或吊销时无服务器数据库处于联机状态，则该数据库也将在大约 10 分钟内变得不可访问，其方式与预配的计算数据库相同。

## <a name="onboarding-into-serverless-compute-tier"></a>载入无服务器计算层

如果要创建新的数据库，或将现有数据库移动到无服务器计算层中，请按照与在预配计算层中创建新数据库时相同的模式操作，其中涉及以下两个步骤。

1. 指定服务目标。 服务目标规定了服务层、硬件代次和最大 vCore 数。 有关服务目标选项，请参阅[无服务器资源限制](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)


2. （可选）指定最小 vCore 数和自动暂停延迟以更改它们的默认值。 下表显示了这些参数可用的值。

   |参数|可选的值|默认值|
   |---|---|---|---|
   |最小 vCore 数|取决于配置的最大 vCore 数 - 请参阅[资源限制](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。|0.5 个 vCore|
   |自动暂停延迟|最低：60 分钟（1 小时）<br>最大值：10080 分钟（7 天）<br>增量：10 分钟<br>禁用自动暂停：-1|60 分钟|


### <a name="create-a-new-database-in-the-serverless-compute-tier"></a>在无服务器计算层中新建数据库

以下示例在无服务器计算层中创建新数据库。

#### <a name="use-the-azure-portal"></a>使用 Azure 门户

请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](single-database-create-quickstart.md)。


#### <a name="use-powershell"></a>使用 PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-the-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>使用 Transact-SQL (T-SQL)

使用 T-SQL 时，最小 vCore 数和自动暂停延迟将应用默认值。

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

有关详细信息，请参阅 [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。  

### <a name="move-a-database-from-the-provisioned-compute-tier-into-the-serverless-compute-tier"></a>将数据库从预配的计算层移到无服务器计算层

以下示例将某个数据库从预配的计算层中移入无服务器计算层。

#### <a name="use-powershell"></a>使用 PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-the-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>使用 Transact-SQL (T-SQL)

使用 T-SQL 时，最小 vCore 数和自动暂停延迟将应用默认值。

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

有关详细信息，请参阅 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)。

### <a name="move-a-database-from-the-serverless-compute-tier-into-the-provisioned-compute-tier"></a>将数据库从无服务器计算层移到预配的计算层

无服务器数据库可以移动到预配计算层中，方法与将预配的计算数据库移动到无服务器计算层相同。

## <a name="modifying-serverless-configuration"></a>修改无服务器配置

### <a name="use-powershell"></a>使用 PowerShell

在 PowerShell 中结合 `MaxVcore`、`MinVcore` 和 `AutoPauseDelayInMinutes` 参数使用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 命令修改最大或最小 vCore 数和自动暂停延迟。

### <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中结合 `capacity`、`min-capacity` 和 `auto-pause-delay` 参数使用 [az sql db update](/cli/azure/sql/db#az-sql-db-update) 命令修改最大或最小 vCore 数和自动暂停延迟。


## <a name="monitoring"></a>监视

### <a name="resources-used-and-billed"></a>已使用和计费的资源

无服务器数据库的资源由应用包、SQL 实例和用户资源池实体封装。

#### <a name="app-package"></a>应用包

应用包是数据库最外层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 应用包包含 SQL 实例和外部服务，这一组合共同限定了所有用户和 SQL 数据库中数据库使用的系统资源的范围。 外部服务的示例包括 R 和全文搜索。 SQL 实例通常决定整个应用包的整体资源利用率。

#### <a name="user-resource-pool"></a>用户资源池

用户资源池是数据库最内层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 用户资源池限定由 DDL 查询（例如 CREATE 和 ALTER）和 DML 查询（例如 SELECT、INSERT、UPDATE 和 DELETE）生成的用户工作负荷的 CPU 和 IO 范围。 这些查询通常表示应用包中最重要的使用率比例。

### <a name="metrics"></a>指标

下表列出了用于监视无服务器数据库应用包和用户池的资源使用情况的指标：

|实体|指标|说明|Units|
|---|---|---|---|
|应用包|app_cpu_percent|应用使用的 vCore 数相对于应用允许的最大 vCore 数的百分比。|百分比|
|应用包|app_cpu_billed|报告期内收取的应用计算费用。 在此期间支付的金额是此指标和 vCore 单位价格的乘积。 <br><br>此指标的值是通过将每秒使用的最大 CPU 和内存使用量按时间进行汇总来得到的。 如果使用的量小于按照最小 vCore 数和最小内存量预配的最小量，则按照预配的最小量进行计费。 为了比较 CPU 与内存以进行计费，可通过将内存量 (GB) 按照每个 vCore 3 GB 进行重新缩放，将内存归一化为以 vCore 数为单位。|vCore 秒|
|应用包|app_memory_percent|应用使用的内存相对于应用允许的最大内存的百分比。|百分比|
|用户池|cpu_percent|用户工作负载使用的 vCore 数相对于用户工作负载允许的最大 vCore 数的百分比。|百分比|
|用户池|data_IO_percent|用户工作负载使用的数据 IOPS 相对于用户工作负载允许的最大数据 IOPS 的百分比。|百分比|
|用户池|log_IO_percent|用户工作负载使用的日志 MB/s 相对于用户工作负载允许的最大日志 MB/s 的百分比。|百分比|
|用户池|workers_percent|用户工作负载使用的工作进程数相对于用户工作负载允许的最大工作进程数的百分比。|百分比|
|用户池|sessions_percent|用户工作负载使用的会话数相对于用户工作负载允许的最大会话数的百分比。|百分比|

### <a name="pause-and-resume-status"></a>暂停和恢复状态

在 Azure 门户中，服务器的概述窗格列出了它所包含的数据库的状态。 数据库状态还显示在该数据库的概述窗格中。

使用以下命令查询数据库的暂停和恢复状态：

#### <a name="use-powershell"></a>使用 PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-the-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>资源限制

有关资源限制的信息，请参阅[无服务器计算层](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。

## <a name="billing"></a>计费

计费的计算量是每秒使用的最大 CPU 和内存量。 如果所用的 CPU 和内存量分别少于最小预配量，则对预配量进行计费。 为了比较 CPU 与内存以进行计费，可通过将内存量 (GB) 按照每个 vCore 3 GB 进行重新缩放，将内存归一化为以 vCore 数为单位。

- **计费的资源**：CPU 和内存
- **计费量**：vCore 单位价格 * 最大值（最小 vCore 数、使用的 vCore 数、最小内存量 (GB) * 1/3、使用的内存量量 (GB) * 1/3） 
- **计费频率**：每秒

vCore 单位价格是每个 vCore 每秒的费用。 请参考 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)，获取给定区域的特定单位价格。

计费的计算量按以下指标进行公开：

- **指标**：app_cpu_billed（vCore 秒）
- **定义**：最大值（最小 vCore 数、使用的 vCore 数、最小内存量(GB) * 1/3、使用的内存量 * 1/3）
- **报告频率**：每分钟

此数量每秒计算一次，按 1 分钟进行汇总。

### <a name="minimum-compute-bill"></a>最小计算费用

如果暂停无服务器数据库，则计算费用将为零。  如果不暂停无服务器数据库，则最小计算费用不少于基于最大值（最小 vCore 数，最小内存 GB * 1/3）的 vCore 数的费用。

示例：

- 假设无服务器数据库没有暂停，并配置有 8 个最大 vCore 和 1 个最小 vCore（对应于 3.0 GB 的最小内存）。  那么，最小计算费用将基于最大值（1 vCore，3.0 GB * 1 vCore / 3 GB）= 1 vCore。
- 假设无服务器数据库没有暂停，并配置有 4 个最大 vCore 和 0.5 个最小 vCore（对应于 2.1 GB 的最小内存）。  那么，最小计算费用基于最大值（0.5 vCore，2.1 GB * 1 vCore / 3 GB）= 0.7 vCore。

无服务器的 [Azure SQL 数据库定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)可用于根据配置的最大和最小 vCore 数来确定可配置的最小内存。  通常，如果配置的最小 vCore 数大于 0.5 个 vCore，则最小计算费用与配置的最小内存无关，仅基于配置的最小 vCore 数。

### <a name="example-scenario"></a>示例方案

假设为某个无服务器数据库配置了最小 vCore 数 1 和最大 vCore 数 4。  这相当于最小内存大约为 3 GB，最大内存大约为 12 GB。  假设自动暂停延迟设置为 6 小时，数据库工作负荷在 24 小时内的前 2 小时处于活动状态，在其他时间处于非活动状态。    

在这种情况下，将会计收数据库在前 8 小时内的计算和存储费用。  尽管数据库在 2 小时后处于非活动状态，但仍会根据预配的最小计算资源，计收数据库联机时的后续 6 小时的计算费用。  当数据库暂停时，只会计收 24 小时时段的剩余时间内的存储费用。

更具体地说，此示例中的计算费用的计算方式如下:

|时间间隔|每秒使用的 vCore 数|每秒使用的 GB 量|计费的计算维度|时间间隔内计费的 vCore 秒数|
|---|---|---|---|---|
|0:00-1:00|4|9|使用的 vCore 数|4 个 vCore * 3600 秒 = 14400 vCore 秒|
|1:00-2:00|1|12|使用的内存量|12 GB * 1/3 * 3600 秒 = 14400 vCore 秒|
|2:00-8:00|0|0|预配的最小内存|3 GB * 1/3 * 21600 秒 = 21600 vCore 秒|
|8:00-24:00|0|0|暂停时不计收计算费用|0 vCore 秒|
|24 小时内计费的总 vCore 秒||||50400 vCore 秒|

假设计算单位价格为 $ 0.000145/vCore/秒。  那么，此24小时内的计算费用是计算单位价格和 vCore 秒计费的产品： $ 0.000145/vCore/second * 50400 vCore 秒 ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure 混合权益和预留容量

Azure 混合权益 (AHB) 和预留容量折扣不适用于无服务器计算层。

## <a name="available-regions"></a>可用区域

无服务器计算层在全球范围内可用，但以下区域除外：中国东部、中国北部、德国中部、德国东北部和 US Gov 中央 (爱荷华) 。

## <a name="next-steps"></a>后续步骤

- 若要入门，请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](single-database-create-quickstart.md)。
- 有关资源限制的信息，请参阅[无服务器计算层资源限制](resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。
