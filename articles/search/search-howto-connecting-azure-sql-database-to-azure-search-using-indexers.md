---
title: "使用索引器将 Azure SQL 数据库连接到 Azure 搜索 | Microsoft Docs"
description: "了解如何使用索引器将数据从 Azure SQL 数据库提取到 Azure 搜索索引。"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 096fcd2a7415da03714f05bb1f29ceac6f186eda
ms.openlocfilehash: dba7cd466d94cb68896ee9270bc765fe822ca00e

---

# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>使用索引器将 Azure SQL 数据库连接到 Azure 搜索
Azure 搜索服务是一项托管的云搜索服务，可以轻松地提供强大的搜索体验。 需要先使用你的数据填充 Azure 搜索索引，然后才能进行搜索。 如果数据驻留在 Azure SQL 数据库中，新 **Azure SQL 数据库的 Azure 搜索索引器**（或简称 **Azure SQL 索引器**）可自动执行索引过程。 这意味着你要编写的代码和关注的基础结构都较少。

本文介绍了使用索引器的机制，但还介绍了仅适用于 Azure SQL 数据库的功能（如集成的更改跟踪）。 Azure 搜索还支持其他数据源，如 Azure DocumentDB、Blob 存储和表存储。 如果想要查看对其他数据源的支持，请在 [Azure 搜索反馈论坛](https://feedback.azure.com/forums/263029-azure-search/)上提供你的反馈。

## <a name="indexers-and-data-sources"></a>索引器和数据源
可使用以下内容设置和配置 Azure SQL 索引器：

* [Azure 门户](https://portal.azure.com)中的导入数据向导
* Azure 搜索 [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)
* Azure 搜索 [REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173)

在本文中，我们将使用 REST API 介绍如何创建和管理**索引器**和**数据源**。

**数据源**指定要编制索引的数据、访问数据所需的凭据和有效标识数据更改（新行、修改的行或删除的行）的策略。 它定义为独立的资源，以便可供多个索引器使用。

**索引器**是将数据源连接到目标搜索索引的资源。 可通过以下方式使用索引器：

* 执行数据的一次性复制以填充索引。
* 按计划使用数据源中的更改更新索引。
* 按需运行以根据需要更新索引。

## <a name="when-to-use-azure-sql-indexer"></a>何时使用 Azure SQL 索引器
根据与你的数据相关的多个因素，可能适合也可能不适合使用 Azure SQL 索引器。 如果你的数据符合以下要求，可以使用 Azure SQL 索引器：

* 所有数据均来自单个表或视图
  * 如果数据分散在多个表中，可以创建一个视图，并将该视图与索引器配合使用。 但是，如果使用视图，则无法使用 SQL Server 集成的更改检测。 有关详细信息，请参阅[此部分](#CaptureChangedRows)。
* 索引器支持数据源中使用的数据类型。 支持大多数但并非全部 SQL 类型。 有关详细信息，请参阅[在 Azure 搜索中映射数据类型](http://go.microsoft.com/fwlink/p/?LinkID=528105)。
* 无需在行发生更改时立即实时更新索引。
  * 索引器最多每 5 分钟重新编制你的表的索引。 如果你的数据频繁更改并且所做更改需要在数秒或数分钟内反映在索引中，建议直接使用 [Azure 搜索索引 API](https://msdn.microsoft.com/library/azure/dn798930.aspx)。
* 如果你具有较大的数据集并打算按计划运行索引器，你的架构允许我们有效标识更改（和删除（如果适用））的行。 有关更多详细信息，请参阅下面的“捕获更改和删除的行”。
* 行中已编制索引的字段的大小不能超过 Azure 搜索索引编制请求的最大大小（即 16 MB）。

## <a name="create-and-use-an-azure-sql-indexer"></a>创建和使用 Azure SQL 索引器
首先，创建数据源：

    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


可从 [Azure 经典门户](https://portal.azure.com)获取连接字符串；使用 `ADO.NET connection string` 选项。

然后，创建目标 Azure 搜索索引（如果还没有）。 可使用[门户 UI](https://portal.azure.com) 或[创建索引 API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 创建索引。 确保你的目标索引的架构与源表的架构兼容 - 请参阅 [SQL 和 Azure 搜索数据类型之间的映射](#TypeMapping)。

最后，通过命名并引用数据源和目标索引创建索引器：

    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

通过此方式创建的索引器不包含计划。 它将在创建后自动运行一次。 可使用**运行索引器**请求随时再次运行：

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

可自定义索引器行为的几个方面，例如批大小和可在索引器执行失败前跳过的文档数。 有关详细信息，请参阅[创建索引器 API](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

你可能需要允许 Azure 服务连接到你的数据库。 有关如何执行该操作的说明，请参阅[从 Azure 连接](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。

若要监视索引器状态和执行历史记录（已编制索引的项目数、失败数等），请使用**索引器状态**请求：

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

响应应类似于以下形式：

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

执行历史记录包含最多 50 个最近完成的执行，它们按反向时间顺序排序（以便最新执行出现在响应中的第一个）。
有关响应的其他信息可在[获取索引器状态](http://go.microsoft.com/fwlink/p/?LinkId=528198)中找到

## <a name="run-indexers-on-a-schedule"></a>按计划运行索引器
还可以排列索引器，以按计划定期运行。 若要执行此操作，在创建或更新索引器时添加**计划**属性。 下面的示例显示了用于更新索引器的 PUT 请求：

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**间隔**参数是必需的。 间隔是指开始两个连续的索引器执行之间的时间。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。

可选 **startTime** 指示应该开始计划执行的时间。 如果省略，则使用当前 UTC 时间。 此时间可以是过去时间，在此情况下计划第一个执行，就像索引器在 startTime 之后连续运行一样。  

一次只能运行一个索引器执行。 如果索引器在计划其执行时运行，该执行将推迟到下一个计划时间。

让我们考虑更具体的示例。 假设我们已配置以下每小时计划：

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

下面是发生的具体情况：

1. 第一个索引器执行于 2015 年 3 月 1 日午夜 12:00 或大约时间开始。 UTC。
2. 假设此执行需要 20 分钟（或小于 1 小时的任何时间）。
3. 第二个执行于 2015 年 3 月 1 日凌晨 1:00 或大约时间开始。
4. 现在，假设此执行需要超过 1 小时（例如 70 分钟），以便在大约凌晨 2:10 完成。
5. 现在是凌晨 2:00，即将开始第三个执行。 但是，由于从凌晨 1:00 开始的第二个执行 仍在运行，将跳过第三个执行。 第三个执行于凌晨 3:00 开始。

可使用 **PUT 索引器请求**针对现有索引器添加、更改或删除计划。

<a name="CaptureChangedRows"></a>

## <a name="capturing-new-changed-and-deleted-rows"></a>捕获新行、更改的行和删除的行
如果表中具有多个行，应使用数据更改检测策略。 通过更改检测，将仅有效检索新行或更改的行，无需重编编制整个表的索引。

### <a name="sql-integrated-change-tracking-policy"></a>SQL 集成的更改跟踪策略
如果你的 SQL 数据库支持[更改跟踪](https://msdn.microsoft.com/library/bb933875.aspx)，我们建议使用 **SQL 集成的更改跟踪策略**。 这是最有效的策略。 此外，它允许 Azure 搜索标识删除的行，无需向你的表中添加显式“软删除”列。

从以下 SQL Server 数据库版本开始，支持集成的更改跟踪：

* SQL Server 2008 R2 及更高版本，如果你使用的是 Azure VM 上的 SQL Server。
* Azure SQL 数据库 V12，如果你使用的是 Azure SQL 数据库。

当使用 SQL 集成的更改跟踪策略时，不指定单独的数据删除检测策略 - 此策略具有对标识删除的行的内置支持。

此策略仅适用于表，不适用于视图。 需要先为要使用的表启用更改跟踪，然后才可以使用此策略。 有关说明，请参阅[启用和禁用更改跟踪](https://msdn.microsoft.com/library/bb964713.aspx)。

若要使用此策略，按如下所示创建或更新数据源：

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>高使用标记更改检测策略
尽管推荐使用 SQL 集成的更改跟踪策略，但它仅适用于表，不适用于视图。 如果你要使用视图，请考虑使用高使用标记策略。 如果你的表或视图包含满足以下条件的列，则可以使用此策略：

* 所有插入都为列指定一个值。
* 对某个项目的所有更新也会更改该列的值。
* 此列的值随每次更改增加。
* 可有效执行使用以下 WHERE 和 ORDER BY 子句进行的查询：`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`。

例如，已编制索引的 **rowversion** 列是高使用标记列的理想候选项。
若要使用此策略，按如下所示创建或更新数据源：

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]"
      }
    }

> [!WARNING]
> 如果源表在高使用标记列上没有索引，SQL 索引器使用的查询可能会超时。 特别是，当表中包含多个行时，`ORDER BY [High Water Mark Column]` 子句需要索引才能有效运行。
>
>

如果遇到超时错误，可以使用 `queryTimeout` 索引器配置设置，将查询超时值设置为高于 5 分钟超时的默认值。 例如，若要将超时设置为 10 分钟，请使用以下配置创建或更新索引器：

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

也可以禁用 `ORDER BY [High Water Mark Column]` 子句。 但是，不建议这样做，因为如果索引器执行由于错误而中断，索引器在将来运行时必须重新处理所有行，即使索引器在中断时就处理了几乎所有行也是如此。 若要禁用 `ORDER BY` 子句，使用索引器定义中的 `disableOrderByHighWaterMarkColumn` 设置：  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>软删除列删除检测策略
从源表中删除行时，你可能还希望从搜索索引中删除这些行。 如果使用 SQL 集成的更改跟踪策略，此操作将自动完成。 但是，高使用标记更改跟踪策略不会帮助你处理删除的行。 怎么办？

如果以物理方式从表中删除行，Azure 搜索无法推断出不再存在的记录是否存在。  但是，可使用“软删除”技术以逻辑方式删除行，无需从表中删除它们。 将列添加到表或视图，并使用该列将行标记为已删除。

使用软删除技术时，可在创建或更新数据源时，按如下方式指定软删除策略：

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** 必须是字符串 - 使用实际值的字符串表示形式。 例如，如果你有一个整数列（使用值 1 标记删除的行），则使用 `"1"`。 如果你有一个 BIT 列（使用布尔值 true 标记删除的行），则使用 `"True"`。

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 数据类型与 Azure 搜索数据类型之间的映射
| SQL 数据类型 | 允许的目标索引字段类型 | 说明 |
| --- | --- | --- |
| bit |Edm.Boolean、Edm.String | |
| int、smallint、tinyint |Edm.Int32、Edm.Int64、Edm.String | |
| bigint |Edm.Int64、Edm.String | |
| real、float |Edm.Double、Edm.String | |
| smallmoney、money decimal numeric |Edm.String |Azure 搜索不支持将十进制类型转换为 Edm.Double，因为这可能会损失精度 |
| char、nchar、varchar、nvarchar |Edm.String<br/>集合 (Edm.String) |如果 SQL 字符串表示 JSON 字符串数组，该字符串可用于填充 Collection(Edm.String) 字段：`["red", "white", "blue"]` |
| smalldatetime、datetime、datetime2、date、datetimeoffset |Edm.DateTimeOffset、Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |仅支持具有 SRID 4326（这是默认值）的类型 POINT 的地理实例 |
| rowversion |不适用 |行版本列不能存储在搜索索引中，但可用于更改跟踪 |
| time、timespan、binary、varbinary、image、xml、geometry、CLR 类型 |不适用 |不支持 |

## <a name="configuration-settings"></a>配置设置
SQL 索引器公开多个配置设置：

| 设置 | 数据类型 | 目的 | 默认值 |
| --- | --- | --- | --- |
| queryTimeout |字符串 |设置 SQL 查询执行的超时 |5 分钟（“00:05:00”） |
| disableOrderByHighWaterMarkColumn |bool |导致高使用标记策略使用的 SQL 查询省略 ORDER BY 子句。 请参阅[高使用标记策略](#HighWaterMarkPolicy) |false |

在索引器定义的 `parameters.configuration` 对象中使用这些设置。 例如，若要将查询超时设置为 10 分钟，请使用以下配置创建或更新索引器：

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>常见问题
**问：**是否可以在 Azure 中将 Azure SQL 索引器与在 IaaS VM 上运行的 SQL 数据库配合使用？

答：是的。 但是，你需要允许搜索服务连接到你的数据库。 有关详细信息，请参阅[配置从 Azure 搜索索引器到 Azure VM 上 SQL Server 的连接](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。

**问：**是否可以将 Azure SQL 索引器与本地运行的 SQL 数据库配合使用？

答：我们不建议也不支持此操作，因为这样做需要你通过 Internet 流量打开你的数据库。

**问：**是否可以在 Azure 上将 Azure SQL 索引器与在 IaaS 中运行的 SQL Server 除外的其他数据库配合使用？

答：我们不支持此方案，因为我们尚未使用 SQL Server 除外的任何数据库测试该索引器。  

**问：**是否可以创建按计划运行的多个索引器？

答：是的。 但是，一次只能在一个节点上运行一个索引器。 如果你需要同时运行多个索引器，请考虑将你的搜索服务扩展到多个搜索单位。

**问：**运行索引器是否会影响我的查询工作负荷？

答：是的。 索引器在你的搜索服务中的一个节点上运行，该节点的资源在编制查询流量索引并进行处理和其他 API 请求之间共享。 如果运行密集型编制索引和查询工作负荷，并频繁遇到 503 错误或响应时间增加，请考虑扩展你的搜索服务。



<!--HONumber=Nov16_HO3-->


