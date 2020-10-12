---
title: 存储概述 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解 Azure 时序见解第 2 代中的数据存储。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: b186c2d2c4b5efc8e1e052a63505549e860b5619
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460822"
---
# <a name="data-storage"></a>数据存储

本文介绍 Azure 时序见解 Gen2 中的数据存储。 它涵盖了热和冷、数据可用性和最佳实践。

## <a name="provisioning"></a>设置

创建 Azure 时序见解 Gen2 环境时，可以使用以下选项：

* 冷数据存储：
  * 在你为环境选择的订阅和区域中创建新的 Azure 存储资源。
  * 附加预先存在的 Azure 存储帐户。 此选项仅可通过从 Azure 资源管理器 [模板](https://docs.microsoft.com/azure/templates/microsoft.timeseriesinsights/allversions)进行部署，并且在 Azure 门户中不可见。
* 热数据存储：
  * 温存储是可选的，可以在预配期间或之后禁用或禁用。 如果你决定稍后启用热存储且你的冷存储中已有数据， [请查看以下](concepts-storage.md#warm-store-behavior) 部分以了解预期的行为。 热存储数据保留时间可以配置为7到31天，也可以根据需要进行调整。

当某个事件处于引入状态时，它会在温存储 (中编制索引（如果启用) 和冷存储）。

[![存储概述](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> 冷存储数据所在 Azure Blob 存储帐户的所有者对该帐户中的所有数据拥有完全访问权限。 此访问权限包括“写入”和“删除”权限。 请不要编辑或删除 Azure 时序见解第 2 代写入的数据，否则可能导致数据丢失。

## <a name="data-availability"></a>数据可用性

为了实现最佳查询性能，Azure 时序见解第 2 代会将数据分区并为其编制索引。 为数据编制索引后，就可以从暖存储（如果已启用）和冷存储中查询数据。 正在引入的数据量以及每个分区的吞吐量速率可能会影响可用性。 查看事件源 [吞吐量限制](./concepts-streaming-ingress-throughput-limits.md) 和 [最佳实践](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) 以获得最佳性能。 你还可以配置 lag [警报](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts) ，以便在你的环境在处理数据时遇到问题时收到通知。

> [!IMPORTANT]
> 最长可能需要等待 60 秒才能看到数据。 如果出现超过 60 秒的明显延迟，请通过 Azure 门户提交支持票证。

## <a name="warm-store"></a>热存储

你的热存储中的数据只能通过 [时序查询 api](./time-series-insights-update-tsq.md)、 [AZURE 时序见解 TSI 资源管理器](./time-series-insights-update-explorer.md)或 [Power BI 连接器](./how-to-connect-power-bi.md)提供。 温存储查询免费，无配额，但并发请求数 [限制为 30](https://docs.microsoft.com/rest/api/time-series-insights/reference-api-limits#query-apis---limits) 。

### <a name="warm-store-behavior"></a>热存储行为

* 启用后，流式传输到您的环境中的所有数据都将路由到您的热存储区，而不考虑事件时间戳。 请注意，流式处理引入管道是为近乎实时的流式处理生成的， [不支持](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)引入的历史事件。
* 保持期基于事件在温存储中的索引时间，而不是事件时间戳。 这意味着，在保持期结束后，即使事件时间戳适用于未来，数据在热存储中也不再可用。
  * 示例：包含10天天气预测的事件在配置了7天保留期的热存储容器中进行引入和索引。 7天后，不能再在温存储中访问预测，但可以从冷查询进行查询。
* 请注意，如果在现有环境中启用了热存储，而该环境已将最近的数据编入索引，则请注意，不会使用此数据填充你的热存储。
* 如果只是启用了热存储，但在浏览器中查看最近的数据时遇到问题，则可以暂时关闭热存储查询：

   [![禁用热查询](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>冷存储

此部分介绍与 Azure 时序见解第 2 代相关的 Azure 存储详细信息。

有关 Azure Blob 存储的全面介绍，请阅读[存储 Blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-cold-storage-account"></a>冷存储帐户

Azure 时序见解第 2 代在 Azure 存储帐户中为每个事件保留最多两个副本。 一个副本存储按引入时间排序的事件，始终允许按时序访问事件。 随着时间的推移，Azure 时序见解第 2 代还会创建数据的重新分区的副本，通过优化实现高性能查询。

你的所有数据无限期存储在 Azure 存储帐户中。

#### <a name="writing-and-editing-blobs"></a>写入和编辑 blob

为了确保查询性能和数据可用性，请不要编辑或删除 Azure 时序见解第 2 代创建的任何 Blob。

#### <a name="accessing-cold-store-data"></a>访问冷存储数据

除了从 [Azure 时序见解资源管理器](./time-series-insights-update-explorer.md) 和 [时序查询 api](./time-series-insights-update-tsq.md)访问数据外，还可能需要直接从存储在 Cold 存储中的 Parquet 文件访问数据。 例如，可以在 Jupyter 笔记本中读取、转换和清理数据，然后使用它来训练同一 Spark 工作流中的 Azure 机器学习模型。

若要直接从 Azure 存储帐户访问数据，你需要具有用于存储 Azure 时序见解第 2 代数据的帐户的读取访问权限。 然后，可以根据 Parquet 文件的创建时间读取选定的数据，该文件位于下面的 [Parquet 文件格式](#parquet-file-format-and-folder-structure)部分所述的 `PT=Time` 文件夹中。  若要详细了解如何启用对存储帐户的读取访问权限，请参阅[管理对存储帐户资源的访问权限](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>数据删除

不要删除你的 Azure 时序见解第 2 代文件。 请只在 Azure 时序见解第 2 代内部管理相关数据。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源的列式文件格式，旨在提高存储效率和性能。 Azure 时序见解第 2 代使用 Parquet 来大规模启用基于时序 ID 的查询的性能。  

有关 Parquet 文件类型的详细信息，请参阅 [Parquet 文档](https://parquet.apache.org/documentation/latest/)。

Azure 时序见解第 2 代按如下方式存储数据的副本：

* 首先，初始副本按引入时间进行分区，并按到达顺序存储数据。 该数据驻留在 `PT=Time` 文件夹中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 其次，重新分区的副本按时序 ID 分组，驻留在 `PT=TsId` 文件夹中：

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

`PT=Time` 文件夹中的 blob 名称中的时间戳对应于数据到达 Azure 时序见解第 2 代的时间，而不是对应于事件的时间戳。

`PT=TsId` 文件夹中的数据会不断针对查询进行优化，不是静态的。 在重新分区期间，某些事件可能会出现在多个 blob 中。 不保证此文件夹中的 blob 的命名保持不变。

通常，如果需要直接通过 Parquet 文件访问数据，请使用 `PT=Time` 文件夹。  将来的功能将能够有效地访问 `PT=TsId` 文件夹。

> [!NOTE]
>
> * `<YYYY>` 映射到 4 位数的年份表示形式。
> * `<MM>` 映射到两位数的月份表示形式。
> * `<YYYYMMDDHHMMSSfff>` 映射到时间戳表示形式，其中包括四位数的年份 (`YYYY`)、两位数的月份 (`MM`)、两位数的日 (`DD`)、两位数的小时 (`HH`)、两位数的分钟 (`MM`)、两位数的秒 (`SS`) 和三位数的毫秒 (`fff`)。

Azure 时序见解第 2 代事件映射到 Parquet 文件内容，如下所示：

* 每个事件映射到单个行。
* 每一行包含带有事件时间戳的**时间戳**列。 “时间戳”属性永远不会为 null。 如果未在事件源中指定时间戳属性，该属性默认为**事件排队时间**。 存储的时间戳始终为 UTC 时间。
* 每一行包含创建 Azure 时序见解第 2 代环境时定义的时序 ID (TSID) 列。 TSID 属性名称包含 `_string` 后缀。
* 作为遥测数据发送的所有其他属性映射到以 `_bool`（布尔值）、`_datetime`（时间戳）、`_long` (long)、`_double` (double)、`_string`（字符串）或 `dynamic` (dynamic) 结尾的列名称，具体取决于属性类型。  有关详细信息，请阅读[支持的数据类型](./concepts-supported-data-types.md)。
* 此映射架构适用于文件格式的第一个版本（以 **V=1** 表示，存储在使用相同名称的基文件夹中）。 随着此功能的演变，此映射架构可能会更改，引用名称的数字会递增。

## <a name="next-steps"></a>后续步骤

* 阅读[数据建模](./time-series-insights-update-tsm.md)。

* 规划 [Azure 时序见解第 2 代环境](./time-series-insights-update-plan.md)。
