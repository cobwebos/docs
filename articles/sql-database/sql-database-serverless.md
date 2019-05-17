---
title: Azure SQL 数据库无服务器（预览版）| Microsoft Docs
description: 本文介绍新的无服务器计算层，并将它与现有的预配计算层进行比较
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/11/2019
ms.openlocfilehash: 7ab22a1d1b44327b28264ec5bd6ba0c44b1d65a7
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620148"
---
# <a name="sql-database-serverless-preview"></a>SQL 数据库无服务器（预览版）

## <a name="what-is-the-serverless-compute-tier"></a>什么是无服务器计算层

SQL 数据库无服务器（预览版）是一个计算层，它按照每秒单一数据库使用的计算量进行计费。 对于具有间歇性、不可预测的使用模式，在空闲使用时间段后的计算预热期间能够容忍一定延迟的单一数据库来说，无服务器是一种高性价比的方案。

无服务器计算层中的数据库由它可以使用的计算范围和自动暂停延迟进行参数化。

![无服务器计费](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>性能

- `MinVcore` 和 `MaxVcore` 是可配置的参数，用于定义数据库可用的计算能力范围。 内存和 IO 限制与指定的 vCore 范围成正比。  
- 自动暂停延迟是可配置的参数，用于定义数据库在自动暂停之前必须处于非活动状态的时间段。 发生下一次登录时，数据库会自动恢复。

### <a name="pricing"></a>定价

- 无服务器数据库的总帐单是计算帐单和存储帐单之和。
计算费用基于使用的 vCore 数和每秒使用的内存量。
- 计费的最小计算量基于最小 vCore 数和最小内存。
- 数据库处于暂停状态时，仅收取存储费用。

## <a name="scenarios"></a>方案

对于具有间歇性、不可预测的使用模式，在空闲使用时间段后的计算预热期间能够容忍一定延迟的单一数据库来说，无服务器是一种高性价比的方案。 与此相反，对于具有较高的平均使用量，并且计算预热期间无法容忍任何延迟的单一或公用数据库，预配计算层是更具性价比的方案。

### <a name="scenarios-well-suited-for-serverless-compute"></a>适合无服务器计算的场景

- 具有间歇性、不可预测的使用模式，中间穿插着不活动的时间段的单一数据库可以从按照每秒使用的计算量计费的价格节约中受益。
- 资源需求难以预测，并且客户倾向于将调整计算大小委托给服务的单一数据库。
- 频繁更改性能级别的预配计算层中的单一数据库。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>适合预配计算的场景

- 随着时间的推移，计算利用率更有规律并且较高的单一数据库。
- 不能容忍因较频繁的内存微调或从暂停状态下自动恢复的延迟，而导致需要对性能做出妥协的数据库。
- 具有间歇性、不可预测的使用模式，可以合并到单个服务器并使用弹性池以实现更好的价格优化的多个数据库。

## <a name="comparison-with-provisioned-compute-tier"></a>与预配计算层的比较

下表汇总了无服务器计算层和预配计算层之间的区别：

| | **无服务器计算** | **预配计算** |
|:---|:---|:---|
|**典型使用场景**| 具有间歇性、不可预测的使用模式，中间穿插着不活动的时间段的数据库。 | 使用情况更有规律的数据库或弹性池。|
| **性能管理工作量** |较低|较高|
|**计算缩放**|自动|手动|
|**计算响应能力**|在非活动期后较低|即时|
|**计费粒度**|每秒|每小时|

## <a name="purchasing-model-and-service-tier"></a>购买模型和服务层级

目前，仅 vCore 购买模型中第 5 代硬件上的常规用途层中支持 SQL 数据库无服务器。

## <a name="autoscaling"></a>自动缩放

### <a name="scaling-responsiveness"></a>缩放响应能力

通常，数据库在具有足够运算能力的计算机上运行，以满足资源需求，而不会中断由 `maxVcores` 值设置的限制范围内任何数量的计算请求。 有时，如果计算机无法在几分钟内满足资源需求，系统便会自动进行负载均衡。 在负载均衡过程中，除了在连接丢失，操作结束时的一小段时间内，数据库将保持联机状态。

### <a name="memory-management"></a>内存管理

无服务器数据库的内存回收比预配数据库的内存回收更加频繁。 这种行为对于在无服务器条件下控制成本非常重要。 与预配计算不同，CPU 或缓存使用率较低时，系统会从无服务器数据库回收 SQL 缓存中的内存。

## <a name="autopause-and-autoresume"></a>自动暂停和自动恢复

### <a name="autopause"></a>自动暂停

如果在自动暂停延迟的时间段内，下面的所有条件均成立，则会触发自动暂停：

- 会话数目 = 0
- CPU = 0（对于在用户池中运行的用户工作负载）

如有需要，系统也提供了禁用自动暂停的选项。

### <a name="autoresume"></a>自动恢复

如果在任何时候，下面的任意条件成立，均会触发自动恢复：

|Feature|自动恢复触发器|
|---|---|
|身份验证和授权|登录|
|威胁检测|启用/禁用数据库或服务器级别的威胁检测设置<br>修改数据库或服务器级别的威胁检测设置|
|数据发现和分类|添加、修改、删除或查看敏感度标签|
|审核|查看审核记录。<br>更新或查看审核策略|
|数据屏蔽|添加、修改、删除或查看数据屏蔽规则|
|透明数据加密|查看透明数据加密的状况或状态|
|查询（性能）数据存储|修改或查看查询存储设置；自动优化|
|自动优化|自动优化建议的应用和验证，例如自动索引|
|数据库复制|创建数据库作为副本<br>导出到 BACPAC 文件|
|SQL 数据同步|按照可配置的时间表或手动执行中心和成员数据库之间的同步|
|修改特定的数据库元数据|添加新的数据库标记<br>更改最大 vCore 数、最小 vCore 数和自动暂停延迟|
|SQL Server Management Studio (SSMS)|使用 SSMS 版本 18 并在服务器中为任意数据库打开新的查询窗口会恢复同一服务器中任何自动暂停的数据库。 如果使用 IntelliSense 处于关闭状态的 SSMS 版本 17.9.1，则不会发生此行为。|

### <a name="connectivity"></a>连接

如果无服务器数据库处于暂停状态，则首次登录将恢复数据库，并返回一个错误，指出数据库将不可用，错误代码为 40613。 恢复数据库后，必须重新尝试登录以建立连接。 具有连接重试逻辑的数据库客户端应该不需要进行修改。

### <a name="latency"></a>Latency

自动暂停或自动恢复无服务器数据库的延迟时间通常为 1 分钟。

### <a name="feature-support"></a>功能支持

以下功能不支持自动暂停和自动恢复。 也就是说，如果使用了以下任意功能，那么无论数据库处于不活动状态的时间长短，数据库都会保持联机状态：

- 异地复制（活动异地复制和自动故障转移组）
- 长期备份保留 (LTR)
- SQL 数据同步中使用的同步数据库。


## <a name="on-boarding-into-the-serverless-compute-tier"></a>载入无服务器计算层

创建新的数据库，或将现有数据库移动到无服务器计算层中的模式与在预配计算层中创建新的数据库相同，均包含以下两个步骤：

1. 指定服务目标名称。 服务目标规定了服务层、硬件代次和最大 vCore 数。 下表显示了服务目标选项：

   |服务目标名称|服务层|硬件代次|最大 vCore 数|
   |---|---|---|---|
   |GP_S_Gen5_1|常规用途|Gen5|第|
   |GP_S_Gen5_2|常规用途|Gen5|2|
   |GP_S_Gen5_4|常规用途|Gen5|4|

2. （可选）指定最小 vCore 数和自动暂停延迟以更改它们的默认值。 下表显示了这些参数可用的值。

   |参数|可选的值|默认值|
   |---|---|---|---|
   |最小 vCore 数|{0.5, 1, 2, 4} 中的任何值，不超过最大 vCore 数|0.5 个 vCore|
   |自动暂停延迟|最小值：360 分钟（6 小时）<br>最大值：10080 分钟（7 天）<br>增量：60 分钟<br>禁用自动暂停：-1|360 分钟|

> [!NOTE]
> 目前不支持使用 T-SQL 将现有数据库移动到无服务器或更改其计算大小，但可以通过 Azure 门户或 PowerShell 完成这些操作。

### <a name="create-new-database-using-the-azure-portal"></a>使用 Azure 门户创建新数据库

请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](sql-database-single-database-get-started.md)。

### <a name="create-new-database-using-powershell"></a>使用 PowerShell 创建新数据库

下面的示例在名为 GP_S_Gen5_4（使用最小 vCore 数和自动暂停延迟的默认值）的服务目标定义的无服务器计算层中创建新数据库。

无服务器需要较新版本的 PowerShell 而不是库中当前的版本，因此运行 `Update-Module Az.Sql` 以获取最新的启用了无服务器的 cmdlet。

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>将现有数据库移动到无服务器计算层

下面的示例将现有单一数据库从预配计算层中移入无服务器计算层。 此示例显式指定最小 vCore 数、最大 vCore 数和自动暂停延迟。

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>将数据库移出无服务器计算层

无服务器数据库可以移动到预配计算层中，方法与将预配的计算数据库移动到无服务器计算层相同。

## <a name="modify-serverless-configuration-parameters"></a>修改无服务器配置参数

### <a name="maximum-vcores"></a>最大 vCore 数

通过在 PowerShell 中使用 [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 命令，使用 `MaxVcore` 参数来修改最大 vCore 数。

### <a name="minimum-vcores"></a>最小 vCore 数

使用修改的最小 Vcore 执行[集 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令，在 PowerShell 中使用`MinVcore`参数。

### <a name="autopause-delay"></a>自动暂停延迟

修改自动暂停延迟执行通过[集 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令，在 PowerShell 中使用`AutoPauseDelay`参数。

## <a name="monitor-serverless-database"></a>监视无服务器数据库

### <a name="resources-used-and-billed"></a>已使用和计费的资源

无服务器数据库的资源由以下实体进行封装：

#### <a name="app-package"></a>应用包

应用包是数据库最外层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 应用包包含 SQL 实例和外部服务，这一组合共同限定了所有用户和 SQL 数据库中数据库使用的系统资源的范围。 外部服务的示例包括 R 和全文搜索。 SQL 实例通常决定整个应用包的整体资源利用率。

#### <a name="user-resource-pool"></a>用户资源池

用户资源池是数据库最内层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 用户资源池限定由 DDL 查询（例如，CREATE、ALTER 等）和 DML 查询（例如 SELECT、INSERT、UPDATE、DELETE 等）带来的用户工作负载的 CPU 和 IO 范围。 这些查询通常表示应用包中最重要的使用率比例。

### <a name="metrics"></a>度量值

|实体|指标|描述|单位|
|---|---|---|---|
|应用包|app_cpu_percent|应用使用的 vCore 数相对于应用允许的最大 vCore 数的百分比。|百分比|
|应用包|app_cpu_billed|报告期内收取的应用计算费用。 在此期间支付的金额是此指标和 vCore 单位价格的乘积。 <br><br>此指标的值是通过将每秒使用的最大 CPU 和内存使用量按时间进行汇总来得到的。 如果使用的量小于按照最小 vCore 数和最小内存量预配的最小量，则按照预配的最小量进行计费。 为了比较 CPU 与内存以进行计费，可通过将内存量 (GB) 按照每个 vCore 3 GB 进行重新缩放，将内存归一化为以 vCore 数为单位。|vCore 秒|
|应用包|app_memory_percent|应用使用的内存相对于应用允许的最大内存的百分比。|百分比|
|用户池|cpu_percent|用户工作负载使用的 vCore 数相对于用户工作负载允许的最大 vCore 数的百分比。|百分比|
|用户池|data_IO_percent|用户工作负载使用的数据 IOPS 相对于用户工作负载允许的最大数据 IOPS 的百分比。|百分比|
|用户池|log_IO_percent|用户工作负载使用的日志 MB/s 相对于用户工作负载允许的最大日志 MB/s 的百分比。|百分比|
|用户池|workers_percent|用户工作负载使用的工作进程数相对于用户工作负载允许的最大工作进程数的百分比。|百分比|
|用户池|sessions_percent|用户工作负载使用的会话数相对于用户工作负载允许的最大会话数的百分比。|百分比|
____

> [!NOTE]
> Azure 门户中单一数据库的指标均位于“监视”下的数据库窗格中。

### <a name="pause-and-resume-status"></a>暂停和恢复状态

在 Azure 门户中，服务器的概述窗格列出了它所包含的数据库的状态。 数据库状态还显示在该数据库的概述窗格中。

使用以下 PowerShell 命令查询数据库的暂停和恢复状态：

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>资源限制

有关资源限制的信息，请参阅[无服务器计算层](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>计费

计费的计算量是每秒使用的最大 CPU 和内存量。 如果所用的 CPU 和内存量分别少于最小预配量，则对预配量进行计费。 为了比较 CPU 与内存以进行计费，可通过将内存量 (GB) 按照每个 vCore 3 GB 进行重新缩放，将内存归一化为以 vCore 数为单位。

- **计费的资源**：CPU 和内存
- **计费的量 ($)**：vCore 单位价格 * 最大值（最小 vCore 数、使用的 vCore 数、最小内存量 (GB) * 1/3、使用的内存量量 (GB) * 1/3） 
- **计费频率**：每秒

vCore 单位价格是每个 vCore 每秒的费用。 请参考 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)，获取给定区域的特定单位价格。

计费的计算量按以下指标进行公开：

- **指标**：app_cpu_billed（vCore 秒）
- **定义**：最大值（最小 vCore 数、使用的 vCore 数、最小内存量(GB) * 1/3、使用的内存量 * 1/3）
- **报告频率**：每分钟

此数量每秒计算一次，按 1 分钟进行汇总。

**示例**：想象一个在一小时内具有以下使用情况且使用 GP_S_Gen5_4 的数据库：

|时间（小时：分钟）|app_cpu_billed（vCore 秒）|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||总计：1631|

假设计算单位的价格为 $0.000073/vCore/秒。 然后，可以使用以下公式确定这一个小时内应付的计算费用：$0.000073/vCore/秒 * 1631 vCore 秒 = $0.1191

## <a name="available-regions"></a>可用区域

无服务器计算层在除以下区域之外的所有区域均可使用：澳大利亚中部、中国东部、中国北部、法国南部、德国中部、德国东北部、印度西部、韩国南部、南非西部、英国北部、英国南部、英国西部和美国中西部

## <a name="next-steps"></a>后续步骤

- 若要开始使用，请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](sql-database-single-database-get-started.md)。
- 有关资源限制的信息，请参阅[无服务器计算层资源限制](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)。
