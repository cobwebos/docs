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
ms.date: 06/12/2019
ms.openlocfilehash: afa575c9015cbb21386d23101b74456822dfa33c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275462"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL 数据库无服务器 （预览版）

每秒使用无服务器 （预览版） 是单一数据库的自动缩放计算基于工作负荷需求并计算量的费用的计算层的 azure SQL 数据库。 无服务器计算层还会自动暂停期间处于非活动状态时唯一的存储进行计费和活动返回时自动恢复数据库的数据库。

## <a name="serverless-compute-tier"></a>无服务器计算层

单一数据库的无服务器计算层是由计算自动缩放范围和 autopause 延迟参数化。  这些参数的配置调整数据库性能体验和计算成本。

![无服务器计费](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>性能配置

- **最小 Vcore**并**最大 vcore 数**个可配置参数来定义的数据库可用的计算能力的范围。 内存和 IO 限制与指定的 vCore 范围成正比。  
- **Autopause 延迟**自动暂停之前定义的时间段的数据库的可配置参数必须为非活动状态。 下一步的登录名或其他活动发生时自动恢复数据库。  或者，可以禁用 autopausing。

### <a name="cost"></a>成本

- 无服务器数据库的成本是计算成本和存储成本的总和。
- 在计算使用量之间的最小值和配置的最大限制时，计算成本基于 vCore 和所用内存。
- 计算使用率配置最小限制以下时，计算成本取决于最小 vcore 数和最小内存配置。
- 数据库暂停时，计算开销为零，并且会产生仅存储成本。
- 如下所示的预配的计算层相同的方式确定的存储成本。

有关成本的详细信息，请参阅[计费](sql-database-serverless.md#billing)。

## <a name="scenarios"></a>方案

对于具有间歇性、不可预测的使用模式，在空闲使用时间段后的计算预热期间能够容忍一定延迟的单一数据库来说，无服务器是一种高性价比的方案。 与此相反，预配的计算层是价格性能优化的单一数据库或更高版本不能承受计算预热中的任何延迟的平均使用率与弹性池中的多个数据库。

### <a name="scenarios-well-suited-for-serverless-compute"></a>适合无服务器计算的场景

- 与处于非活动状态并随时间推移的较低平均计算使用率的高峰期混杂的单一数据库与断断续续的、 不可预测的使用模式。
- 经常重新缩放的预配的计算层和客户的首选委托中的单个数据库计算重新缩放到服务。
- 新的单一数据库，而无需计算大小调整其中很难或不可能在 SQL 数据库中的部署前估计的使用情况历史记录。

### <a name="scenarios-well-suited-for-provisioned-compute"></a>适合预配计算的场景

- 单一数据库与多个常规、 可预测的使用模式和较高的平均计算使用率随着时间的推移。
- 不能容忍因较频繁的内存微调或从暂停状态下自动恢复的延迟，而导致需要对性能做出妥协的数据库。
- 包含可以合并到更好的价格性能优化的弹性池的断断续续的、 不可预测的使用模式的多个数据库。

## <a name="comparison-with-provisioned-compute-tier"></a>与预配计算层的比较

下表汇总了无服务器计算层和预配计算层之间的区别：

| | **无服务器计算** | **预配计算** |
|:---|:---|:---|
|**数据库使用模式**| 使用较低的平均计算使用率随着时间的推移断断续续的、 不可预测使用情况。 |  多个常规使用模式，具有较高的平均计算随时间或使用弹性池的多个数据库的利用率。|
| **性能管理工作量** |较低|较高|
|**计算缩放**|自动|手动|
|**计算响应能力**|在非活动期后较低|即时|
|**计费粒度**|每秒|每小时|

## <a name="purchasing-model-and-service-tier"></a>购买模型和服务层级

目前，仅 vCore 购买模型中第 5 代硬件上的常规用途层中支持 SQL 数据库无服务器。

## <a name="autoscaling"></a>自动缩放

### <a name="scaling-responsiveness"></a>缩放响应能力

一般情况下，无服务器数据库是在计算机上运行具有足够的容量来满足资源请求而不发生中断的任意数量的请求的最大 vcore 数值设置的限制范围内的计算。 有时，如果计算机无法在几分钟内满足资源需求，系统便会自动进行负载均衡。 例如，如果资源需求是 4 个 Vcore，但只有 2 个 Vcore 都可用，然后它可能需要几分钟时间进行负载平衡之前提供 4 个 Vcore。 在负载均衡过程中，除了在连接丢失，操作结束时的一小段时间内，数据库将保持联机状态。

### <a name="memory-management"></a>内存管理

无服务器数据库的内存回收更频繁地比的预配的计算数据库。 此行为是重要中无服务器的控制成本，并且可能会影响性能。

#### <a name="cache-reclamation"></a>缓存回收

预配的计算与数据库不同，从 SQL 缓存的内存被回收从无服务器数据库时 CPU 或缓存使用率较低。

- 最多的总大小最近的一段时间内使用缓存条目低于阈值时，缓存使用率被视为低。
- 触发缓存回收后，目标缓存大小以增量方式缩减为其以前的大小的一小部分，并回收仅继续使用情况保持较低水平。
- 当缓存回收发生时，选择要逐出缓存项的策略是与预配的计算数据库的同一选择策略内存压力较高时。
- 缓存大小永远不会减少下面规定的最小 Vcore，可配置的最小内存限制。

在无服务器和预配计算数据库，可能会逐出项，如果使用所有可用内存的缓存。

#### <a name="cache-hydration"></a>缓存资源混合速度

从提取数据磁盘的速度相同预配数据库具有相同的方式以及随着 SQL 缓存。 当数据库忙碌时，缓存可以增长不受约束，直至达到最大内存限制。

## <a name="autopausing-and-autoresuming"></a>Autopausing 和 autoresuming

### <a name="autopausing"></a>Autopausing

如果所有以下条件成立 autopause 延迟的持续时间内，则触发 Autopausing:

- 会话数目 = 0
- CPU = 用户运行的工作负荷中的用户池 0

若要禁用 autopausing，如果需要，提供了一个选项。

以下功能不支持 autopausing。  也就是说，如果使用了以下功能，然后在数据库保持联机无论数据库处于不活动状态的持续时间：

- 异地复制 （活动异地复制和自动故障转移组）。
- 长期备份保留 (LTR)。
- SQL 数据同步中使用的同步数据库。

在要求数据库处于联机状态的一些服务更新部署过程中暂时阻止 Autopausing。  在这种情况下，autopausing 将成为服务更新完成后再次允许。

### <a name="autoresuming"></a>Autoresuming

如果以下任何条件成立在任何时候，会触发 Autoresuming:

|Feature|自动恢复触发器|
|---|---|
|身份验证和授权|登录|
|威胁检测|启用/禁用在数据库或服务器级别的威胁检测设置。<br>修改数据库或服务器级别的威胁检测设置。|
|数据发现和分类|添加、修改、删除或查看敏感度标签|
|审核|查看审核记录。<br>更新或查看审核策略。|
|数据屏蔽|添加、修改、删除或查看数据屏蔽规则|
|透明数据加密|查看透明数据加密的状况或状态|
|查询（性能）数据存储|修改或查看查询存储设置；自动优化|
|自动优化|自动优化建议的应用和验证，例如自动索引|
|数据库复制|作为副本创建数据库。<br>导出到 BACPAC 文件。|
|SQL 数据同步|按照可配置的时间表或手动执行中心和成员数据库之间的同步|
|修改特定的数据库元数据|添加数据库的新标记。<br>更改最大 vcore 数、 最小 Vcore 或自动暂停延迟。|
|SQL Server Management Studio (SSMS)|使用 SSMS 版本 18 并在服务器中为任意数据库打开新的查询窗口会恢复同一服务器中任何自动暂停的数据库。 如果使用 IntelliSense 处于关闭状态的 SSMS 版本 17.9.1，则不会发生此行为。|

在要求数据库处于联机状态的一些服务更新部署过程中，也会触发 Autoresuming。

### <a name="connectivity"></a>连接

如果暂停无服务器数据库，然后将恢复数据库首次登录并返回一个错误，指出数据库将不可用，错误代码 40613。 恢复数据库后，必须重新尝试登录以建立连接。 具有连接重试逻辑的数据库客户端应该不需要进行修改。

### <a name="latency"></a>Latency

Autoresume 和 autopause 无服务器数据库连接的延迟时间通常是 1 分钟到 autoresume 和 1-10 分钟以便自动暂停。

## <a name="onboarding-into-serverless-compute-tier"></a>载入到无服务器计算层级

创建新的数据库或移动到无服务器计算层的现有数据库遵循相同的模式与创建的新数据库中预配计算层，包括以下两个步骤。

1. 指定服务目标名称。 服务目标规定了服务层、 硬件代次和最大 vcore 数。 下表显示了服务目标选项：

   |服务目标名称|服务层|硬件代次|最大 vCore 数|
   |---|---|---|---|
   |GP_S_Gen5_1|常规用途|Gen5|第|
   |GP_S_Gen5_2|常规用途|Gen5|2|
   |GP_S_Gen5_4|常规用途|Gen5|4|

2. （可选） 指定最小 vcore 数和 autopause 延迟，若要更改其默认值。 下表显示了这些参数可用的值。

   |参数|可选的值|默认值|
   |---|---|---|---|
   |最小 Vcore|{0.5, 1, 2, 4} 中的任何值，不超过最大 vCore 数|0.5 个 vCore|
   |自动暂停延迟|最低：360 分钟（6 小时）<br>最大值：10080 分钟（7 天）<br>增量：60 分钟<br>禁用自动暂停：-1|360 分钟|

> [!NOTE]
> 目前不支持使用 T-SQL 将现有数据库移动到无服务器或更改其计算大小，但可以通过 Azure 门户或 PowerShell 完成这些操作。

### <a name="create-new-database-in-serverless-compute-tier"></a>无服务器计算层中创建新的数据库 

#### <a name="use-azure-portal"></a>使用 Azure 门户

请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](sql-database-single-database-get-started.md)。

#### <a name="use-powershell"></a>使用 PowerShell

下面的示例在无服务器计算层中创建新的数据库。  此示例显式指定最小 vCore 数、最大 vCore 数和自动暂停延迟。

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
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>将数据库从预配的计算层移到无服务器计算层

#### <a name="use-powershell"></a>使用 PowerShell

下面的示例将数据库从预配的计算层移到无服务器计算层。 此示例显式指定最小 vCore 数、最大 vCore 数和自动暂停延迟。

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
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>将数据库从无服务器计算层移到预配的计算层

无服务器数据库可以移动到预配计算层中，方法与将预配的计算数据库移动到无服务器计算层相同。

## <a name="modifying-serverless-configuration"></a>修改无服务器配置

### <a name="maximum-vcores"></a>最大 vCore 数

#### <a name="use-powershell"></a>使用 PowerShell

修改最大 vcore 数使用执行[集 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令，在 PowerShell 中使用`MaxVcore`参数。

### <a name="minimum-vcores"></a>最小 vCore 数

#### <a name="use-powershell"></a>使用 PowerShell

使用修改的最小 Vcore 执行[集 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令，在 PowerShell 中使用`MinVcore`参数。

### <a name="autopause-delay"></a>自动暂停延迟

#### <a name="use-powershell"></a>使用 PowerShell

修改自动暂停延迟执行通过[集 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)命令，在 PowerShell 中使用`AutoPauseDelayInMinutes`参数。

## <a name="monitoring"></a>监视

### <a name="resources-used-and-billed"></a>已使用和计费的资源

通过应用程序包、 SQL 实例和用户资源池实体封装的无服务器数据库的资源。

#### <a name="app-package"></a>应用包

应用包是数据库最外层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 应用包包含 SQL 实例和外部服务，这一组合共同限定了所有用户和 SQL 数据库中数据库使用的系统资源的范围。 外部服务的示例包括 R 和全文搜索。 SQL 实例通常决定整个应用包的整体资源利用率。

#### <a name="user-resource-pool"></a>用户资源池

用户资源池是数据库最内层的资源管理边界，无论数据库位于无服务器计算层还是预配计算层中。 用户资源池范围 CPU 和 IO 用户工作负荷，如生成的 DDL 查询，如 CREATE 和 ALTER 和 DML 查询用于选择、 插入、 更新和删除。 这些查询通常表示应用包中最重要的使用率比例。

### <a name="metrics"></a>度量值

下表列出了用于监视无服务器数据库的应用包和用户池的资源使用情况指标：

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
- **计费金额**: vCore 单价 * 最大值 (最小 Vcore、 使用的 vcore 数、 最小内存 GB * 1/3，内存使用的 GB * 1/3) 
- **计费频率**：每秒

在每个 vCore 每秒费用 vCore 单位价格。 请参考 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)，获取给定区域的特定单位价格。

计费的计算量按以下指标进行公开：

- **指标**：app_cpu_billed（vCore 秒）
- **定义**：最大值（最小 vCore 数、使用的 vCore 数、最小内存量(GB) * 1/3、使用的内存量 * 1/3）
- **报告频率**：每分钟

此数量每秒计算一次，按 1 分钟进行汇总。

请考虑配置了 1 的最小 vCore 和 4 的最大 vcore 数的无服务器数据库。  这对应于大约 3 GB 内存的最小值和 12 GB 最大内存。  假设自动暂停延迟设置为 6 小时和数据库工作负荷处于活动状态 24 小时内的第一个 2 小时内，否则为处于非活动状态。    

在这种情况下，数据库是为计算和存储计费期间第一个 8 小时。  即使数据库处于非活动启动第二个小时后，它仍按照基于预配数据库处于联机状态时的最小计算后续 6 小时内的计算的。  暂停数据库时，仅存储计费期间在 24 小时内的其余部分。

更确切地说，在此示例中计算帐单的计算方式如下：

|时间间隔|使用每个第二个 Vcore|GB 使用每秒|计算维度计费|vCore 秒的时间间隔内计费|
|---|---|---|---|---|
|0:00-1:00|4|9|使用 Vcore|4 个 Vcore * 3600 秒 = 14400 vCore 秒|
|1:00-2:00|第|12|使用内存|12 GB * 1/3 * 3600 秒 = 14400 vCore 秒|
|2:00-8:00|0|0|预配的最小内存|3 GB * 1/3 * 21600 秒 = 21600 vCore 秒|
|8:00-24:00|0|0|计费暂停时没有计算|0 vCore 秒|
|总 vCore 秒计费在 24 小时||||50400 vCore 秒|

假设计算单位的价格为 $0.000073/vCore/秒。  然后收取此 24 小时内的计算是计费的计算单位价格和 vCore 秒的产品： $0.000073/vCore/second * 50400 vCore 秒 = $3.68

## <a name="available-regions"></a>可用区域

无服务器计算层是全球可用的除以下区域：澳大利亚中部、 中国东部、 中国北部、 法国南部、 德国中部、 德国东北部、 印度西部、 韩国南部、 南非西部、 英国北部、 英国南部、 英国西部和美国中西部。

## <a name="next-steps"></a>后续步骤

- 若要开始使用，请参阅[快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库](sql-database-single-database-get-started.md)。
- 有关资源限制的信息，请参阅[无服务器计算层资源限制](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)。
