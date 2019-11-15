---
title: Azure 数据资源管理器与 Azure 数据工厂的集成
description: 本主题将 Azure 数据资源管理器与 Azure 数据工厂集成，以使用复制、查找和命令活动
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: dd2b3bd584bb39810e0a5c9acde1a961330c273d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093756"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>将 Azure 数据资源管理器与 Azure 数据工厂集成

[Azure 数据工厂](/azure/data-factory/)（ADF）是一项基于云的数据集成服务，可用于集成不同的数据存储并对数据执行活动。 ADF 允许创建数据驱动的工作流，用于协调和自动化数据移动和数据转换。 Azure 数据资源管理器是 Azure 数据工厂中[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)之一。 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure 数据资源管理器的 azure 数据工厂活动

Azure 数据工厂的各种集成适用于 Azure 数据资源管理器用户：

### <a name="copy-activity"></a>复制活动
 
Azure 数据工厂复制活动用于在数据存储之间传输数据。 Azure 数据资源管理器支持为源，其中数据从 Azure 数据资源管理器复制到任何受支持的数据存储和接收器，其中数据从任何支持的数据存储复制到 Azure 数据资源管理器。 有关详细信息，请参阅[使用 Azure 数据工厂向/从 azure 数据资源管理器复制数据](/azure/data-factory/connector-azure-data-explorer)。 有关详细的演练，请参阅将[数据从 Azure 数据工厂加载到 azure 数据资源管理器](data-factory-load-data.md)。
当在 Azure 中复制数据时，以及在使用访问控制（如 Azure 虚拟网络）从本地或网络中复制数据时，使用 azure 数据资源管理器受 Azure IR （Integration Runtime）的支持。 有关详细信息，请参阅[使用哪个 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> 使用复制活动和创建**链接服务**或**数据集**时，请选择数据存储**Azure 数据资源管理器（Kusto）** 而不是旧的数据存储**Kusto**。  

### <a name="lookup-activity"></a>查找活动
 
查找活动用于在 Azure 数据资源管理器上执行查询。 查询的结果将作为查找活动的输出返回，并可用于管道中的下一个活动，如[ADF 查找文档](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)中所述。  
除了5000行和 2 MB 的响应大小限制之外，活动还会将查询超时限制值限制为1小时。

### <a name="command-activity"></a>命令活动

命令活动允许执行 Azure 数据资源管理器[控制命令](/azure/kusto/concepts/#control-commands)。 与查询不同，控件命令可能会修改数据或元数据。 某些控制命令的目标是使用 `.ingest`或 `.set-or-append`等命令将数据引入 Azure 数据资源管理器中，或使用 `.export`等命令将数据从 Azure 数据资源管理器复制到外部数据存储。
有关命令活动的详细演练，请参阅[使用 Azure 数据工厂命令活动运行 azure 数据资源管理器控制命令](data-factory-command-activity.md)。  有时，使用 control 命令复制数据比复制活动更快且更便宜。 若要确定何时使用 Command 活动与复制活动，请参阅[在复制数据时在复制和命令活动之间选择](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)。

### <a name="copy-in-bulk-from-a-database-template"></a>从数据库模板大容量复制

[使用 Azure 数据工厂模板从数据库批量复制到 azure 数据资源管理器](data-factory-template.md)是预定义的 Azure 数据工厂管道。 此模板用于创建每个数据库或每个表的多个管道，以便加快数据复制。 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>复制数据时，选择 "复制" 和 "Azure 数据资源管理器" 命令活动 

本部分将帮助你为数据复制需求选择正确的活动。

在将数据从或复制到 Azure 数据资源管理器时，Azure 数据工厂中有两个可用的选项：
* 复制活动。
* Azure 数据资源管理器 Command 活动，该活动执行在 Azure 数据资源管理器中传输数据的一个控制命令。 

### <a name="copy-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器复制数据
  
可以使用复制活动或[`.export`](/azure/kusto/management/data-export/)命令从 Azure 数据资源管理器复制数据。 `.export` 命令执行查询，然后导出查询结果。 

请参阅下表，了解复制活动和用于从 Azure 数据资源管理器复制数据的 `.export` 命令。

| | 复制活动 | 导出命令 |
|---|---|---|
| **流说明** | ADF 在 Kusto 上执行查询，处理结果，并将其发送到目标数据存储。 <br>（**ADX > ADF > 接收器数据存储**） | ADF 将 `.export` control 命令发送到 Azure 数据资源管理器，后者执行命令，并将数据直接发送到目标数据存储。 <br>（**ADX > 接收器数据存储**） |
| **支持的目标数据存储** | 各种[受支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2，Azure Blob，SQL 数据库 |
| **性能** | 集中 | <ul><li>分布式（默认值），从多个节点同时导出数据</li><li>更快且更具成本效益。</li></ul> |
| **服务器限制** | 可以扩展/禁用[查询限制](/azure/kusto/concepts/querylimits)。 默认情况下，ADF 查询包含： <ul><li>500000记录的大小限制或 64 MB。</li><li>时间限制为10分钟。</li><li>`noTruncation` 设置为 false。</li></ul> | 默认情况下，扩展或禁用查询限制： <ul><li>禁用大小限制。</li><li>服务器超时延长到1小时。</li><li>`MaxMemoryConsumptionPerIterator` 和 `MaxMemoryConsumptionPerQueryPerNode` 扩展到 max （5 GB，TotalPhysicalMemory/2）。</li></ul>

> [!TIP]
> 如果你的复制目标是 `.export` 命令支持的数据存储之一，并且任何复制活动功能都不能满足你的需求，请选择 `.export` 命令。

### <a name="copying-data-to-azure-data-explorer"></a>将数据复制到 Azure 数据资源管理器

可以使用复制活动或引入命令（例如从[查询](/azure/kusto/management/data-ingestion/ingest-from-query)中引入（`.set-or-append`、`.set-or-replace`、`.set`、`.replace)`和[从存储](/azure/kusto/management/data-ingestion/ingest-from-storage)引入）将数据复制到 Azure 数据资源管理器。`.ingest` 

请参阅下表，了解复制活动的比较，并引入用于将数据复制到 Azure 数据资源管理器的命令。

| | 复制活动 | 从查询引入<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 从存储引入 <br> `.ingest` |
|---|---|---|---|
| **流说明** | ADF 从源数据存储中获取数据，将其转换为表格格式，并执行所需的架构映射更改。 然后，ADF 将数据上传到 Azure blob，将其拆分为块区，然后下载 blob 以将它们引入到 ADX 表中。 <br> （**源数据存储 > ADF > Azure blob > ADX**） | 这些命令可以执行查询或 `.show` 命令，并将查询的结果引入表中（**ADX > ADX**）。 | 此命令通过 "提取" 一个或多个云存储项目中的数据，将数据引入表中。 |
| **支持的源数据存储** |  [各种选项](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS 第2代、Azure Blob、SQL （使用 sql_request 插件）、Cosmos （使用 cosmosdb_sql_request 插件）以及提供 HTTP 或 Python Api 的任何其他数据存储。 | Filesystem，Azure Blob 存储，ADLS 第1代，ADLS 第2代 |
| **性能** | Ingestions 已排队和管理，可通过提供负载均衡、重试和错误处理来确保小型 Ingestions 并确保高可用性。 | <ul><li>这些命令不能用于大容量数据导入。</li><li>按预期和更便宜的方式工作。 但对于生产方案以及流量速率和数据量大时，请使用复制活动。</li></ul> |
| **服务器限制** | <ul><li>无大小限制。</li><li>最大超时限制：每个引入 blob 1 小时。 |<ul><li>查询部分只有大小限制，可通过指定 `noTruncation=true`来跳过该部分。</li><li>最大超时限制：1小时。</li></ul> | <ul><li>无大小限制。</li><li>最大超时限制：1小时。</li></ul>|

> [!TIP]
> * 从 ADF 向 Azure 复制数据时数据资源管理器使用 `ingest from query` 命令。  
> * 对于大型数据集（> 1GB），请使用复制活动。  

## <a name="required-permissions"></a>所需的权限

下表列出了与 Azure 数据工厂集成的各个步骤所需的权限。

| 步骤 | Operation | 最低权限级别 | 说明 |
|---|---|---|---|
| **创建链接服务** | 数据库导航 | *数据库查看器* <br>使用 ADF 的登录用户应该获得读取数据库元数据的权限。 | 用户可以手动提供数据库名称。 |
| | 测试连接 | *数据库监视器*或*表引入器* <br>应授予服务主体 `.show` 命令或表级别引入执行数据库级别的权限。 | <ul><li>TestConnection 验证与群集的连接，而不是与数据库的连接。 即使数据库不存在，也可能会成功。</li><li>表管理员权限不足。</li></ul>|
| **创建数据集** | 表导航 | *数据库监视器* <br>使用 ADF 登录的用户必须有权执行数据库级别 `.show` 命令。 | 用户可以手动提供表名称。|
| **创建数据集**或**复制活动** | 预览数据 | *数据库查看器* <br>必须授权服务主体读取数据库元数据。 | | 
|   | 导入架构 | *数据库查看器* <br>必须授权服务主体读取数据库元数据。 | 当 ADX 是表格到表格副本的源时，ADF 会自动导入架构，即使用户没有显式导入架构也是如此。 |
| **ADX 作为接收器** | 创建按名称列映射 | *数据库监视器* <br>必须授权服务主体 `.show` 命令执行数据库级别。 | <ul><li>所有必需的操作都适用于*表引入器*。</li><li> 某些可选操作可能会失败。</li></ul> |
|   | <ul><li>在表上创建 CSV 映射</li><li>删除映射</li></ul>| *表引入器*或*数据库管理* <br>必须授权服务主体对表进行更改。 | |
|   | 引入数据 | *表引入器*或*数据库管理* <br>必须授权服务主体对表进行更改。 | | 
| **作为源的 ADX** | 执行查询 | *数据库查看器* <br>必须授权服务主体读取数据库元数据。 | |
| **Kusto 命令** | | 根据每个命令的权限级别。 |

## <a name="performance"></a>性能 

如果 Azure 数据资源管理器是源，并使用包含查询的 "查找"、"复制" 或 "命令" 活动，请参阅有关复制活动的性能信息和[ADF 文档](/azure/data-factory/copy-activity-performance)的[查询最佳实践](/azure/kusto/query/best-practices)。
  
本部分介绍 Azure 数据资源管理器是接收器的复制活动的使用。 Azure 数据资源管理器接收器的估计吞吐量为 11-13 MBps。 下表详细说明了影响 Azure 数据资源管理器接收器性能的参数。

| 参数 | 说明 |
|---|---|
| **组件地理位置邻近** | 将所有组件置于同一区域：<ul><li>源和接收器数据存储。</li><li>ADF 集成运行时。</li><li>你的 ADX 群集。</li></ul>请确保至少集成运行时与 ADX 群集位于同一区域。 |
| **DIUs 数** | 1个 VM，适用于 ADF 使用的每4个 DIUs。 <br>如果源是包含多个文件的基于文件的存储，则增加 DIUs 将会有所帮助。 然后，每个 VM 将并行处理不同的文件。 因此，复制单个大型文件比复制多个较小的文件时的延迟更高。|
|**ADX 群集的数量和 SKU** | 大量 ADX 节点将提高引入处理时间。|
| **度** | 若要从数据库复制大量数据，请对数据进行分区，然后使用以并行方式复制每个分区或使用[从数据库大容量复制到 Azure 数据资源管理器模板](data-factory-template.md)的 ForEach 循环。 注意： "复制" 活动中的 "**设置**" > **并行度**与 ADX 无关。 |
| **数据处理复杂性** | 根据源文件格式、列映射和压缩，延迟会有所不同。|
| **运行集成运行时的 VM** | <ul><li>对于 Azure 副本，无法更改 ADF Vm 和计算机 Sku。</li><li> 对于 "本地到 Azure" 副本，确定托管自承载 IR 的 VM 是否足够强。</li></ul>|

## <a name="monitor-activity-progress"></a>监视活动进度

* 当监视活动进度时，"*数据写入*" 属性可能比 "*数据读取*" 属性大得多，因为*数据读取*是根据二进制文件大小计算的，而*写入*的数据会根据内存中的大小计算，然后再对数据进行反序列化和解压缩。

* 监视活动进度时，可以看到数据已写入 Azure 数据资源管理器接收器。 查询 Azure 数据资源管理器表时，你会看到数据尚未到达。 这是因为，在复制到 Azure 数据资源管理器时有两个阶段。 
    * 第一阶段读取源数据，将其拆分为 900 MB，并将每个区块上传到 Azure Blob。 第一阶段显示在 ADF 活动进度视图中。 
    * 将所有数据上传到 Azure Blob 后，第二个阶段开始。 Azure 数据资源管理器引擎节点下载 blob 并将数据引入接收器表。 然后，Azure 数据资源管理器表中会显示该数据。

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 数据工厂将数据复制到 azure 数据资源管理器](data-factory-load-data.md)。
* 了解如何使用[Azure 数据工厂模板从数据库大容量复制到 Azure 数据资源管理器](data-factory-template.md)。
* 了解如何使用[Azure 数据工厂命令活动运行 azure 数据资源管理器控制命令](data-factory-command-activity.md)。
* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。



