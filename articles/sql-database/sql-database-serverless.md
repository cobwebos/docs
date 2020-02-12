---
title: 无服务器
description: 本文介绍新的无服务器计算层，并将它与现有的预配计算层进行比较
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: 750d08f3667317e9e1e396cff50884101d7ff55d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131966"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL 数据库无服务器

Azure SQL 数据库无服务器是单一数据库的计算层，可根据工作负荷需求和帐单自动根据每秒使用的计算量来缩放计算。 当只对存储计费并在活动返回时自动恢复数据库时，无服务器计算层还会在非活动期间自动暂停数据库。

## <a name="serverless-compute-tier"></a>无服务器计算层

单个数据库的无服务器计算层由计算自动缩放范围和 autopause 延迟参数化。  这些参数的配置将形成数据库性能经验和计算成本。

![无服务器计费](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>性能配置

- **最小 vcore**和**最大 vcore**是用于定义可用于数据库的计算容量范围的可配置参数。 内存和 IO 限制与指定的 vCore 范围成正比。  
- **Autopause 延迟**是一个可配置参数，用于定义数据库在自动暂停之前必须处于非活动状态的时间段。 当下一个登录或其他活动发生时，数据库将自动恢复。  或者，可以禁用 autopausing。

### <a name="cost"></a>成本

- 无服务器数据库的成本是计算成本与存储成本的总和。
- 如果计算使用量介于配置的最小和最大限制之间，计算开销将基于使用的 vCore 和内存。
- 如果计算使用量低于配置的最小限制，计算开销将基于配置的最小 Vcore 和最小内存。
- 暂停数据库后，计算开销为零，且仅会产生存储费用。
- 存储开销的确定方式与预配计算层中的相同。

有关更多成本的详细信息，请参阅[计费](sql-database-serverless.md#billing)。

## <a name="scenarios"></a>方案

对于具有间歇性、不可预测的使用模式，在空闲使用时间段后的计算预热期间能够容忍一定延迟的单一数据库来说，无服务器是一种高性价比的方案。 与此相反，预配的计算层是为单一数据库或弹性池中的多个数据库优化的价格性能，具有较高的平均使用量，在计算预热中不能承受任何延迟。

### <a name="scenarios-well-suited-for-serverless-compute"></a>适合无服务器计算的场景

- 具有间歇、不可预测的使用模式的单一数据库，在一段时间内与非活动期和平均计算利用率发生了下降。
- 预配计算层中的单个数据库，通常是重新缩放的，并且客户更倾向于将计算重新缩放委托给该服务。
- 不使用使用情况历史记录的新单一数据库，计算规模很难或不可能在 SQL 数据库中部署之前进行估计。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>适合预配计算的场景

- 单个数据库，具有更常规、可预测的使用模式和更高的平均计算使用率。
- 不能容忍因较频繁的内存微调或从暂停状态下自动恢复的延迟，而导致需要对性能做出妥协的数据库。
- 具有间歇、不可预测的使用模式的多个数据库，可合并到弹性池中以实现更好的价格性能优化。

## <a name="comparison-with-provisioned-compute-tier"></a>与预配计算层的比较

下表汇总了无服务器计算层和预配计算层之间的区别：

| | **无服务器计算** | **预配计算** |
|:---|:---|:---|
|**数据库使用模式**| 一段时间内的平均计算利用率，具有间歇的不可预测使用情况。 |  使用更高的平均计算使用率（一段时间）或使用弹性池的多个数据库的更常规使用模式。|
| **性能管理工作量** |Lower|较高|
|**计算缩放**|自动|手动|
|**计算响应能力**|在非活动期后较低|即时|
|**计费粒度**|每秒|每小时|

## <a name="purchasing-model-and-service-tier"></a>购买模型和服务层级

目前，仅 vCore 购买模型中第 5 代硬件上的常规用途层中支持 SQL 数据库无服务器。

## <a name="autoscaling"></a>自动扩展

### <a name="scaling-responsiveness"></a>缩放响应能力

通常，无服务器数据库在具有足够容量的计算机上运行，以满足资源需求，而不会中断在最大 Vcore 值设置的限制内请求的任何数量的计算。 有时，如果计算机无法在几分钟内满足资源需求，系统便会自动进行负载均衡。 例如，如果资源需求为 4 Vcore，但只有2个 Vcore 可用，则在提供4个 Vcore 之前，可能需要几分钟的负载平衡。 在负载均衡过程中，除了在连接丢失，操作结束时的一小段时间内，数据库将保持联机状态。

### <a name="memory-management"></a>内存管理

针对无服务器数据库的内存回收更频繁于预配的计算数据库。 此行为对于控制无服务器开销并可能影响性能非常重要。

#### <a name="cache-reclamation"></a>缓存回收

与预配的计算数据库不同，当 CPU 或缓存使用率较低时，从无服务器数据库中回收 SQL 缓存中的内存。

- 如果最近使用的缓存条目的总大小低于某个时间段的阈值，则将缓存利用率视为低。
- 当触发缓存回收时，目标缓存大小会增量降低到以前大小的一小部分，并且仅当使用率保持较低时才继续。
- 发生缓存回收时，选择要逐出的缓存条目的策略与预配计算数据库的策略相同，因为在内存压力较高时。
- 缓存大小永远不会小于 min Vcore 定义的最小内存限制（可配置）。

在无服务器和预配的计算数据库中，如果使用了所有可用内存，则可能会逐出缓存条目。

#### <a name="cache-hydration"></a>缓存混合

SQL 缓存的增长，因为数据是以相同的方式从磁盘中提取的，并且速度与预配的数据库相同。 当数据库处于繁忙状态时，允许缓存增大到最大内存限制。

## <a name="autopausing-and-autoresuming"></a>Autopausing 和 autoresuming

### <a name="autopausing"></a>Autopausing

如果在 autopause 延迟的持续时间内满足以下所有条件，则会触发 Autopausing：

- 会话数目 = 0
- 在用户池中运行的用户工作负荷的 CPU = 0

如果需要，将提供一个选项来禁用 autopausing。

以下功能不支持 autopausing。  也就是说，如果使用以下任何功能，则数据库将保持联机状态，而不考虑数据库的非活动持续时间：

- 异地复制（活动异地复制和自动故障转移组）。
- 长期备份保留（LTR）。
- SQL 数据同步中使用的同步数据库。 与同步数据库不同，中心和成员数据库支持 autopausing。
- 弹性作业中使用的作业数据库。

在部署某些需要数据库联机的服务更新的过程中，暂时会阻止 Autopausing。  在这种情况下，服务更新完成后，将再次允许 autopausing。

### <a name="autoresuming"></a>Autoresuming

如果以下任一条件为 true，则会触发 Autoresuming：

|功能|自动恢复触发器|
|---|---|
|身份验证和授权|Login|
|威胁检测|启用/禁用数据库或服务器级别的威胁检测设置。<br>修改数据库或服务器级别的威胁检测设置。|
|数据发现和分类|添加、修改、删除或查看敏感度标签|
|审核|查看审核记录。<br>更新或查看审核策略。|
|数据屏蔽|添加、修改、删除或查看数据屏蔽规则|
|透明数据加密|查看透明数据加密的状况或状态|
|查询（性能）数据存储|修改或查看 query store 设置|
|自动优化|自动优化建议的应用和验证，例如自动索引|
|数据库复制|以副本形式创建数据库。<br>导出到 BACPAC 文件。|
|SQL 数据同步|按照可配置的时间表或手动执行中心和成员数据库之间的同步|
|修改特定的数据库元数据|正在添加新的数据库标记。<br>更改 max Vcore、min Vcore 或 autopause delay。|
|SQL Server Management Studio (SSMS)|使用早于18.1 的 SSMS 版本，并为服务器中的任何数据库打开新的查询窗口，将恢复同一服务器中的任何自动暂停的数据库。 如果使用 SSMS 18.1 版或更高版本，则不会发生此行为。|

在部署某些需要数据库联机的服务更新的过程中，也会触发 Autoresuming。

### <a name="connectivity"></a>连接

如果暂停无服务器数据库，则第一次登录会恢复数据库并返回错误，指出该数据库不可用，错误代码为40613。 恢复数据库后，必须重新尝试登录以建立连接。 具有连接重试逻辑的数据库客户端应该不需要进行修改。

### <a name="latency"></a>延迟

无服务器数据库的 autoresume 和 autopause 延迟通常按1分钟到 autoresume 和1-10 分钟到 autopause。

## <a name="onboarding-into-serverless-compute-tier"></a>载入无服务器计算层

创建新数据库或将现有数据库移到无服务器计算层的模式与在预配的计算层中创建新数据库的模式相同，其中包括以下两个步骤。

1. 指定服务目标名称。 服务目标规定服务层、硬件生成和最大 Vcore。 下表显示了服务目标选项：

   |服务目标名称|服务层|硬件代次|最大 vCore 数|
   |---|---|---|---|
   |GP_S_Gen5_1|常规用途|Gen5|1|
   |GP_S_Gen5_2|常规用途|Gen5|2|
   |GP_S_Gen5_4|常规用途|Gen5|4|
   |GP_S_Gen5_6|常规用途|Gen5|6|
   |GP_S_Gen5_8|常规用途|Gen5|8|
   |GP_S_Gen5_10|常规用途|Gen5|10|
   |GP_S_Gen5_12|常规用途|Gen5|12|
   |GP_S_Gen5_14|常规用途|Gen5|14|
   |GP_S_Gen5_16|常规用途|Gen5|16|

2. 还可以指定最小 Vcore 和 autopause 延迟，以更改其默认值。 下表显示了这些参数可用的值。

   |参数|可选的值|默认值|
   |---|---|---|---|
   |最小 Vcore|取决于配置的最大 Vcore-请参阅[资源限制](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。|0.5 个 vCore|
   |自动暂停延迟|最小值：60分钟（1小时）<br>最大值：10080分钟（7天）<br>增量：60分钟<br>禁用自动暂停：-1|60 分钟|


### <a name="create-new-database-in-serverless-compute-tier"></a>在无服务器计算层中创建新数据库 

以下示例在无服务器计算层中创建新数据库。 这些示例显式指定 min Vcore、max Vcore 和 autopause delay。

#### <a name="use-azure-portal"></a>使用 Azure 门户

请参阅[快速入门：使用 Azure 门户在 AZURE SQL 数据库中创建单个数据库](sql-database-single-database-get-started.md)。


#### <a name="use-powershell"></a>使用 PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>使用 Transact-sql （T-sql）

以下示例在无服务器计算层中创建新数据库。

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

有关详细信息，请参阅[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)。  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>将数据库从预配的计算层移到无服务器计算层

下面的示例将数据库从预配的计算层移到无服务器计算层。 这些示例显式指定 min Vcore、max Vcore 和 autopause delay。

#### <a name="use-powershell"></a>使用 PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>使用 Transact-sql （T-sql）

下面的示例将数据库从预配的计算层移到无服务器计算层。

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

有关详细信息，请参阅[ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)。

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>将数据库从无服务器计算层移到预配的计算层

无服务器数据库可以移动到预配计算层中，方法与将预配的计算数据库移动到无服务器计算层相同。

## <a name="modifying-serverless-configuration"></a>修改无服务器配置

### <a name="use-powershell"></a>使用 PowerShell

通过使用 `MaxVcore`、`MinVcore`和 `AutoPauseDelayInMinutes` 参数，在 PowerShell 中使用[AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)命令修改最大值或最小值 vcore 和 autopause 延迟。

### <a name="use-azure-cli"></a>使用 Azure CLI

通过使用 `capacity`、`min-capacity`和 `auto-pause-delay` 参数在 Azure CLI 中使用[az sql db update](/cli/azure/sql/db#az-sql-db-update)命令来修改最大值或最小值 vcore 和 autopause 延迟。


## <a name="monitoring"></a>监视

### <a name="resources-used-and-billed"></a>已使用和计费的资源

无服务器数据库的资源由应用包、SQL 实例和用户资源池实体封装。

#### <a name="app-package"></a>应用包

应用包是数据库最外层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 应用包包含 SQL 实例和外部服务，这一组合共同限定了所有用户和 SQL 数据库中数据库使用的系统资源的范围。 外部服务的示例包括 R 和全文搜索。 SQL 实例通常决定整个应用包的整体资源利用率。

#### <a name="user-resource-pool"></a>用户资源池

用户资源池是数据库最内层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 用户资源池通过 DDL 查询生成的用户工作负荷（例如，SELECT、INSERT、UPDATE 和 DELETE 等）生成的 CPU 和 IO。 这些查询通常表示应用包中最重要的使用率比例。

### <a name="metrics"></a>度量值

下表列出了用于监视应用包和无服务器数据库的用户池资源使用情况的指标：

|实体|跃点数|说明|单位|
|---|---|---|---|
|应用包|app_cpu_percent|应用使用的 vCore 数相对于应用允许的最大 vCore 数的百分比。|百分比|
|应用包|app_cpu_billed|报告期内收取的应用计算费用。 在此期间支付的金额是此指标和 vCore 单位价格的乘积。 <br><br>此指标的值是通过将每秒使用的最大 CPU 和内存使用量按时间进行汇总来得到的。 如果使用的量小于按照最小 vCore 数和最小内存量预配的最小量，则按照预配的最小量进行计费。 为了将 CPU 与内存进行比较以便于计费，将内存中的内存量（以 GB 为单位）重新调整为每个 vCore 的 Vcore，以将内存标准化为多个单元。|vCore 秒|
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

#### <a name="use-azure-cli"></a>使用 Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>资源限制

有关资源限制，请参阅[无服务器计算层](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。

## <a name="billing"></a>计费

计费的计算量是每秒使用的最大 CPU 和内存量。 如果所用的 CPU 和内存量分别少于最小预配量，则对预配量进行计费。 为了比较 CPU 与内存以进行计费，可通过将内存量 (GB) 按照每个 vCore 3 GB 进行重新缩放，将内存归一化为以 vCore 数为单位。

- **资源计费**： CPU 和内存
- **计费金额**： vCore 单位价格 * 最大值（最小值为 vcore，已使用 vcore，最小内存 gb * 1/3，使用的内存 gb * 1/3） 
- **计费频率**：每秒

VCore 单位价格是每秒 vCore 的成本。 请参考 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)，获取给定区域的特定单位价格。

计费的计算量按以下指标进行公开：

- **指标**：app_cpu_billed（vCore 秒）
- **定义**：最大值（最小 vCore 数、使用的 vCore 数、最小内存量(GB) * 1/3、使用的内存量 * 1/3）
- **报告频率**：每分钟

此数量每秒计算一次，按 1 分钟进行汇总。

请考虑一个配置有1分钟 vCore 和 4 max Vcore 的无服务器数据库。  这对应于 3 GB 的最小内存和 12 GB 的最大内存。  假设自动暂停延迟设置为6小时，数据库工作负荷在24小时内的前2小时处于活动状态，否则为非活动状态。    

在这种情况下，数据库在前8小时内按计算和存储计费。  即使在第二个小时后数据库处于不活动状态，也会根据在数据库处于联机状态时所设置的最小计算，在后续6小时内对计算进行计费。  在数据库暂停期间，在24小时内的剩余时间内，只会对存储计费。

更准确地说，此示例中的计算帐单按如下方式计算：

|时间间隔|每秒使用的 Vcore|每秒使用的 GB 数|计算维度已计费|vCore 按时间间隔计费的秒数|
|---|---|---|---|---|
|0:00-1:00|4|9|使用的 Vcore|4 Vcore * 3600 秒 = 14400 vCore 秒|
|1:00-2:00|1|12|使用的内存|12 GB * 1/3 * 3600 秒 = 14400 vCore 秒|
|2:00-8:00|0|0|最小内存预配|3 GB * 1/3 * 21600 秒 = 21600 vCore 秒|
|8:00-24:00|0|0|暂停时未计费计算|0 vCore 秒|
|每24小时计费的 vCore 秒总数||||50400 vCore 秒|

假设计算单位价格为 $ 0.000145/vCore/秒。  那么，此24小时内的计算费用是计算单位价格和 vCore 秒计费的产品： $ 0.000145/vCore/second * 50400 vCore 秒 ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure 混合权益和预留容量

Azure 混合权益（AHB）和保留容量折扣不适用于无服务器计算层。

## <a name="available-regions"></a>可用区域

"无服务器" 计算层在全球范围内可用，但以下区域除外：中国东部、中国北部、德国中部、德国东北部、英国北部、英国南部2、美国西部和 US Gov 中部（爱荷华）。

## <a name="next-steps"></a>后续步骤

- 若要开始操作，请参阅[快速入门：使用 Azure 门户在 AZURE SQL 数据库中创建单个数据库](sql-database-single-database-get-started.md)。
- 有关资源限制的信息，请参阅[无服务器计算层资源限制](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)。
