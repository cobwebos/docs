---
title: Azure 数据资源管理器与 Azure 数据工厂的集成
description: 本主题介绍如何将 Azure 数据资源管理器与 Azure 数据工厂集成，以使用复制、查找和命令活动
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293617"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>将 Azure 数据资源管理器与 Azure 数据工厂集成

[Azure 数据工厂](/azure/data-factory/) (ADF) 是基于云的数据集成服务，可用于集成不同的数据存储，以及对数据执行活动。 使用 ADF 可以创建数据驱动式工作流用于协调和自动化数据移动与数据转换。 Azure 数据资源管理器是 Azure 数据工厂中[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)之一。 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure 数据资源管理器的 Azure 数据工厂活动

Azure 数据资源管理器用户可以使用 Azure 数据工厂的各种集成：

### <a name="copy-activity"></a>复制活动
 
Azure 数据工厂复制活动用于在数据存储之间传输数据。 支持将 Azure 数据资源管理器用作源，在这种情况下，数据将从 Azure 数据资源管理器复制到任何受支持的数据存储；也可以将它用作接收器，在这种情况下，数据将从任何受支持的数据存储复制到 Azure 数据资源管理器。 有关详细信息，请参阅使用[Azure 数据工厂将数据复制到 Azure 数据资源管理器或从 Azure 数据资源管理器](/azure/data-factory/connector-azure-data-explorer)中复制数据。 有关详细演练，请参阅[将数据从 Azure 数据工厂载入 Azure 数据资源管理器](data-factory-load-data.md)。
Azure IR (Integration Runtime) 支持使用 Azure 数据资源管理器在 Azure 内部复制数据，自承载 IR 支持使用 Azure 数据资源管理器从/向本地或配置了访问控制的网络（例如 Azure 虚拟网络）中的数据存储复制数据。 有关详细信息，请参阅[要使用哪个 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> 使用复制活动以及创建**链接服务**或**数据集**时，请选择数据存储“Azure 数据资源管理器(Kusto)”，而不要选择旧数据存储“Kusto”。********  

### <a name="lookup-activity"></a>查找活动
 
查找活动用于在 Azure 数据资源管理器中执行查询。 查询结果将作为查找活动的输出返回，可以在管道中的下一个活动中使用，具体如 [ADF 查找文档](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)中所述。  
除了将响应大小限制为 5,000 行和 2 MB 以外，该活动还将查询超时限制为 1 小时。

### <a name="command-activity"></a>命令活动

使用命令活动可以执行 Azure 数据资源管理器[控制命令](/azure/kusto/concepts/#control-commands)。 与查询不同，控制命令可能会修改数据或元数据。 某些控制命令旨在通过 `.ingest` 或 `.set-or-append` 等命令将数据引入 Azure 数据资源管理器，或通过 `.export` 等命令将数据从 Azure 数据资源管理器复制到外部数据存储。
有关命令活动的详细演练，请参阅[使用 Azure 数据工厂命令活动运行 Azure 数据资源管理器控制命令](data-factory-command-activity.md)。  使用控制命令复制数据有时比使用复制活动更快且更节省。 若要确定何时使用命令活动或复制活动，请参阅[复制数据时在复制活动与命令活动之间进行选择](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)。

### <a name="copy-in-bulk-from-a-database-template"></a>从数据库模板批量复制

[使用 Azure 数据工厂模板从数据库批量复制到 Azure 数据资源管理器](data-factory-template.md)是一个预定义的 Azure 数据工厂管道。 使用该模板可针对每个数据库或每个表创建多个管道，以更快地复制数据。 

### <a name="mapping-data-flows"></a>映射数据流 

[Azure 数据工厂映射数据流](/azure/data-factory/concepts-data-flow-overview)是可视化设计的数据转换，允许数据工程师无需编写代码即可开发图形数据转换逻辑。 要创建数据流并将数据引入 Azure 数据资源管理器，请使用以下方法：

1. 创建[映射数据流](/azure/data-factory/data-flow-create)。
1. [将数据导出到 Azure Blob](/azure/data-factory/data-flow-sink)。 
1. 定义[事件网格](/azure/data-explorer/ingest-data-event-grid)或[ADF 复制活动](/azure/data-explorer/data-factory-load-data)以将数据引入 Azure 数据资源管理器。

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>复制数据时在复制活动与 Azure 数据资源管理器命令活动之间进行选择 

本部分帮助你根据数据复制需求选择正确的活动。

从/向 Azure 数据资源管理器复制数据时，可以使用 Azure 数据工厂中的两个选项：
* 复制活动。
* Azure 数据资源管理器命令活动，该活动执行某个控制命令来传输 Azure 数据资源管理器中的数据。 

### <a name="copy-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器复制数据
  
可以使用复制活动或[`.export`](/azure/kusto/management/data-export/)命令从 Azure 数据资源管理器复制数据。 `.export` 命令执行某个查询，然后导出该查询的结果。 

下表比较了用于从 Azure 数据资源管理器复制数据的复制活动和 `.export` 命令。

| | 复制活动 | .export 命令 |
|---|---|---|
| **流程说明** | ADF 对 Kusto 执行查询，处理结果，然后将结果发送到目标数据存储。 <br>（**ADX > ADF > 接收器数据存储**） | ADF 将 `.export` 控制命令发送到 Azure 数据资源管理器，后者执行该命令，然后将数据直接发送到目标数据存储。 <br>（**ADX > 接收器数据存储**） |
| **支持的目标数据存储** | 有多种[支持的数据存储](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2、Azure Blob、SQL 数据库 |
| **性能** | 集中式 | <ul><li>分布式（默认配置），从多个节点同时导出数据</li><li>速度更快，COGS 成本效益更高。</li></ul> |
| **服务器限制** | 可以提高/禁用[查询限制](/azure/kusto/concepts/querylimits)。 默认情况下，ADF 查询包含： <ul><li>500,000 条记录或 64 MB 的大小限制。</li><li>10 分钟时间限制。</li><li>`noTruncation` 设置为 false。</li></ul> | 默认情况下，可提高或禁用查询限制： <ul><li>禁用大小限制。</li><li>将服务器超时提高至 1 小时。</li><li>`MaxMemoryConsumptionPerIterator` 和 `MaxMemoryConsumptionPerQueryPerNode` 提高至最大值（5 GB，TotalPhysicalMemory/2）。</li></ul>

> [!TIP]
> 如果复制目标是 `.export` 命令支持的数据存储之一，并且没有任何复制活动功能对于满足需求而言至关重要，请选择 `.export` 命令。

### <a name="copying-data-to-azure-data-explorer"></a>将数据复制到 Azure 数据资源管理器

可以使用复制活动或引入命令（如`.set``.replace)``.ingest``.set-or-append``.set-or-replace`[从查询（、、、](/azure/kusto/management/data-ingestion/ingest-from-query)和[从存储（）引入](/azure/kusto/management/data-ingestion/ingest-from-storage)来将数据复制到 Azure 数据资源管理器。 

下表比较了用于将数据复制到 Azure 数据资源管理器的复制活动和引入命令。

| | 复制活动 | 从查询引入<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 从存储引入 <br> `.ingest` |
|---|---|---|---|
| **流程说明** | ADF 从源数据存储获取数据，将数据转换为表格格式，并执行所需的架构映射更改。 然后，ADF 将数据上传到 Azure Blob，将数据拆分为区块，然后下载 Blob 以将其引入 ADX 表中。 <br> （**源数据存储 > ADF > Azure Blob > ADX**） | 这些命令可以执行查询或 `.show` 命令，并将查询结果引入表中 (**ADX > ADX**)。 | 此命令通过从一个或多个云存储项目“提取”数据，将数据引入表中。 |
| **支持的源数据存储** |  [多种选项](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2、Azure Blob、SQL（使用 sql_request 插件）、Cosmos（使用 cosmosdb_sql_request 插件），以及提供 HTTP 或 Python API 的任何其他数据存储。 | Filesystem、Azure Blob 存储、ADLS Gen 1、ADLS Gen 2 |
| **性能** | 引入内容将会排队并受到管理，确保实现小规模的引入，并通过提供负载均衡、重试和错误处理来确保高可用性。 | <ul><li>这些命令并不旨在用于导入大量数据。</li><li>它们可按预期方式运行，且成本低廉。 但是，对于生产方案以及在流量速率和数据量较大时，请使用复制活动。</li></ul> |
| **服务器限制** | <ul><li>无大小限制。</li><li>最大超时限制：每个引入的 blob 1 小时。 |<ul><li>只有查询部分存在大小限制，可通过指定 `noTruncation=true` 来跳过该限制。</li><li>最大超时限制：1 小时。</li></ul> | <ul><li>无大小限制。</li><li>最大超时限制：1 小时。</li></ul>|

> [!TIP]
> * 将数据从 ADF 复制到 Azure 数据资源管理器时，请使用 `ingest from query` 命令。  
> * 对于大型数据集 (>1GB)，请使用复制活动。  

## <a name="required-permissions"></a>所需的权限

下表列出了执行与 Azure 数据工厂集成的各个步骤所需的权限。

| 步骤 | Operation | 最低权限级别 | 说明 |
|---|---|---|---|
| **创建链接服务** | 数据库导航 | *数据库查看器* <br>使用 ADF 的已登录用户需获得授权才能读取数据库元数据。 | 用户可以手动提供数据库名称。 |
| | 测试连接 | 数据库监视者或表引入者**** <br>服务主体需获得授权才能执行数据库级别的 `.show` 命令或表级别的引入。 | <ul><li>TestConnection 验证与群集的连接，而不验证与数据库的连接。 即使数据库不存在，此命令也可能会成功。</li><li>拥有表管理员权限并不足够。</li></ul>|
| **创建数据集** | 表导航 | *数据库监视器* <br>使用 ADF 的已登录用户必须获得授权才能执行数据库级别的 `.show` 命令。 | 用户可以手动提供表名称。|
| **创建数据集**或**复制活动** | 预览数据 | *数据库查看器* <br>服务主体必须获得授权才能读取数据库元数据。 | | 
|   | 导入架构 | *数据库查看器* <br>服务主体必须获得授权才能读取数据库元数据。 | 当 ADX 是表格到表格复制操作的源时，ADF 会自动导入架构，即使用户未显式导入架构。 |
| **ADX 用作接收器** | 创建按名称的列映射 | *数据库监视器* <br>服务主体必须获得授权才能执行数据库级别的 `.show` 命令。 | <ul><li>所有必需的操作将使用“表引入者”角色。**</li><li> 某些可选操作可能会失败。</li></ul> |
|   | <ul><li>在表中创建 CSV 映射</li><li>删除映射</li></ul>| 表引入者或数据库管理员**** <br>服务主体必须获得授权才能对表进行更改。 | |
|   | 引入数据 | 表引入者或数据库管理员**** <br>服务主体必须获得授权才能对表进行更改。 | | 
| **ADX 用作源** | 执行查询 | *数据库查看器* <br>服务主体必须获得授权才能读取数据库元数据。 | |
| **Kusto 命令** | | 根据每个命令的权限级别。 |

## <a name="performance"></a>性能 

如果 Azure 数据资源管理器是源，而你使用包含 where 查询的查找、复制或命令活动，请参阅[查询最佳做法](/azure/kusto/query/best-practices)了解性能信息，并参阅[复制活动的 ADF 文档](/azure/data-factory/copy-activity-performance)。
  
本部分介绍如何使用将 Azure 数据资源管理器用作接收器的复制活动。 Azure 数据资源管理器接收器的估计吞吐量为 11-13 MBps。 下表详细描述了影响 Azure 数据资源管理器接收器性能的参数。

| 参数 | 说明 |
|---|---|
| **组件的地理邻近性** | 将所有组件放在同一区域：<ul><li>源和接收器数据存储。</li><li>ADF 集成运行时。</li><li>ADX 群集。</li></ul>确保最起码你自己的集成运行时位于 ADX 群集所在的同一区域。 |
| **DIU 数** | ADF 使用的每 4 个 DIU 有 1 个 VM。 <br>仅当源是包含多个文件的基于文件的存储时，增加 DIU 才有作用。 然后，每个 VM 将并行处理一个不同的文件。 因此，复制单个大文件比复制多个小文件的延迟更高。|
|**ADX 群集的数量和 SKU** | 使用大量的 ADX 节点会大幅增加引入处理时间。|
| **并行** | 若要从数据库中复制大量数据，请将数据分区，然后使用 ForEach 循环并行复制每个分区，或使用[“从数据库批量复制到 Azure 数据资源管理器”模板](data-factory-template.md)。 注意：**Settings** > 复制活动中**的并行性设置程度**与 ADX 无关。 |
| **数据处理复杂性** | 延迟根据源文件格式、列映射和压缩而有所不同。|
| **运行集成运行时的 VM** | <ul><li>对于 Azure 复制，无法更改 ADF VM 和计算机 SKU。</li><li> 对于本地到 Azure 的复制，请确定托管自承载 IR 的 VM 是否足够强大。</li></ul>|

## <a name="tips-and-common-pitfalls"></a>提示和常见错误

### <a name="monitor-activity-progress"></a>监视活动进度

* 监视活动进度时，“写入的数据”属性可能比“读取的数据”属性要大得多，因为“读取的数据”是根据二进制文件大小计算的，而“写入的数据”是在反序列化并解压缩数据后，根据内存中大小计算的。********

* 监视活动进度时，可以看到数据已写入 Azure 数据资源管理器接收器。 查询 Azure 数据资源管理器表时，会看到数据尚未抵达。 这是因为，复制到 Azure 数据资源管理器的过程是分两个阶段完成的。 
    * 第一阶段读取源数据，将数据拆分为 900-MB 的区块，然后将每个区块上传到 Azure Blob。 ADF 活动进度视图会显示第一阶段。 
    * 将所有数据上传到 Azure Blob 后，第二个阶段随即开始。 Azure 数据资源管理器引擎节点下载 Blob，并将数据引入接收器表。 然后，Azure 数据资源管理器表中会显示这些数据。

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>因转义不当而无法引入 CSV 文件

Azure 数据资源管理器要求 CSV 文件遵循 [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt)。
它要求：
* 包含需要转义的字符（例如 " 和新行）的字段应以 **"** 字符开头和结尾，不使用空格。 字段中的所有 **"** 字符** 都使用双 **"** 字符 (**""**) 进行转义。 例如，_"Hello, ""World"""_ 是一个有效的 CSV 文件，所包含的单个记录有一个内容为 _Hello, "World"_ 的列或字段。
* 文件中的所有记录必须有相同的列数和字段数。

Azure 数据工厂允许反斜杠（转义）字符。 如果使用 Azure 数据工厂生成一个包含反斜杠字符的 CSV 文件，则无法将该文件引入 Azure 数据资源管理器。

#### <a name="example"></a>示例

以下文本值：你好，"世界"<br/>
ABC   DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

应出现在适当的 CSV 文件中，如下所示："你好，"世界"<br/>
"ABC   DEF"<br/>
"""ABC DEF"<br/>
"""ABC\D""EF"<br/>

通过使用默认转义字符（反斜杠），以下 CSV 将无法使用 Azure 数据资源管理器："你好，\"世界"\"<br/>
"ABC   DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>嵌套的 JSON 对象

将 JSON 文件复制到 Azure 数据资源管理器时，请注意：
* 不支持数组。
* 如果 JSON 结构包含对象数据类型，Azure 数据工厂会将对象的子项平展，并尝试将每个子项映射到 Azure 数据资源管理器表中的不同列。 若要将整个对象项映射到 Azure 数据资源管理器中的单个列，请执行以下操作：
    * 将整个 JSON 行引入 Azure 数据资源管理器中的单个动态列。
    * 使用 Azure 数据工厂的 JSON 编辑器手动编辑管道定义。 在“映射”中，执行以下操作：****
       * 删除为每个子项创建的多个映射，并添加单个可将对象类型映射到表列的映射。
       * 在右方括号后面添加一个逗号，后跟：<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>在复制到 Azure 数据资源管理器时指定 AdditionalProperties

> [!NOTE]
> 目前，手动编辑 JSON 有效负载即可使用此功能。 

在复制活动的“sink”节下添加单个行，如下所示：

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

对值的转义操作可能很微妙。 请使用以下代码片段作为参考：

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

输出的值：

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 数据工厂将数据复制到 Azure 数据资源管理器](data-factory-load-data.md)。
* 了解如何[使用 Azure 数据工厂模板从数据库批量复制到 Azure 数据资源管理器](data-factory-template.md)。
* 了解如何[使用 Azure 数据工厂命令活动运行 Azure 数据资源管理器控制命令](data-factory-command-activity.md)。
* 了解用于查询数据的 [Azure 数据资源管理器查询](/azure/data-explorer/web-query-data)。



