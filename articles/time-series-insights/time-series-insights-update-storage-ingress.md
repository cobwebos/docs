---
title: 数据存储和入口（预览版）- Azure 时序见解 | Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据存储和入口。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: ca5ba8d7b2d78440401e29344361538c3650ba48
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779168"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据存储和入口

本文介绍 Azure 时序见解预览版中对数据存储和入口的更新。 本文介绍底层存储结构、文件格式和时序 ID 属性。 此外，还介绍了底层入口流程、最佳做法和当前预览限制。

## <a name="data-ingress"></a>数据入口

Azure 时序见解环境包含一个*引入引擎*，用于收集、处理和存储时序数据。

[规划环境](time-series-insights-update-plan.md)时需要遵循一些注意事项，以确保处理所有传入数据，实现高入口规模，并最大程度地降低引入延迟时间（时序见解从事件源读取和处理数据所用的时间）。

时序见解预览版数据入口策略确定数据的来源位置以及数据应采用的格式。

### <a name="ingress-policies"></a>入口策略

数据引入涉及到将数据发送到 Azure 时序见解预览版环境的方式。

下面总结了密钥配置、格式设置和最佳做法。

#### <a name="event-sources"></a>事件源

Azure 时序见解预览版支持以下事件源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

Azure 时序见解预览版针对每个实例最多支持两个事件源。 连接事件源时，TSI 环境会从最早的事件开始，读取当前存储在 Iot 或事件中心中的所有事件。

> [!IMPORTANT]
>
> * 将事件源附加到预览环境时，可能会发生较高的初始延迟。
> 事件源延迟取决于当前在 IoT 中心或事件中心内的事件数。
> * 在事件源数据第一次引入后，高延迟会降低。 如果遇到持续的高延迟，请通过 Azure 门户提交支持票证。

#### <a name="supported-data-format-and-types"></a>支持的数据格式和类型

Azure 时序见解支持从 Azure IoT 中心或 Azure 事件中心发送的 UTF-8 编码的 JSON。 

支持的数据类型有：

| 数据类型 | 说明 |
|---|---|
| **bool** | 具有以下两种状态之一的数据类型：`true` 或 `false`。 |
| **dateTime** | 表示时间上的一刻，通常以日期和当天的时间表示。 用 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 |
| **double** | 双精度 64 位 [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 浮点。 |
| **string** | 文本值，包含 Unicode 字符。          |

#### <a name="objects-and-arrays"></a>对象和数组

可以将复杂类型（如对象和数组）作为事件有效负载的一部分发送，但在存储数据时，数据将经历一个平展过程。

如需详细了解如何调整 JSON 事件、发送复杂类型和嵌套的对象平展，请参阅[如何为入口和查询调整 JSON](./time-series-insights-update-how-to-shape-events.md)，以辅助规划和优化。

### <a name="ingress-best-practices"></a>入口最佳做法

建议使用以下最佳做法：

* 在同一区域中配置 Azure 时序见解和任何 IoT 中心或事件中心，以减少潜在的延迟。

* [计算预计引入率并验证其是否处于以下所列支持的比率范围内，从而规划规模需求](time-series-insights-update-plan.md)。

* 阅读[如何为入口和查询调整 JSON](./time-series-insights-update-how-to-shape-events.md)，了解如何优化和调整 JSON 数据以及预览版的当前限制。

* 流式传输引入仅限用于近实时数据和最新数据，不支持流式传输历史数据。

#### <a name="historical-data-ingestion"></a>历史数据引入

Azure 时序见解预览版目前不支持使用流式传输管道导入历史数据。 如果需要将过去的数据导入到环境中，请遵循以下准则：

* 不要并行传输实时和历史数据。 引入无序数据将导致查询性能下降。
* 按时间顺序引入历史数据，以获得最佳性能。
* 不超过以下引入吞吐率上限。
* 如果数据早于你的 Warm 存储保留期，请禁用 Warm 存储。

### <a name="ingress-scale-and-preview-limitations"></a>入口规模和预览版限制

Azure 时序见解预览版入口限制如下所述。

> [!TIP]
> 阅读[规划预览环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)以获取所有预览限制的综合列表。

#### <a name="per-environment-limitations"></a>基于环境的限制

通常，入口速率被视为组织中的设备数、事件发射频率和每个事件大小的系数：

*  设备数 × 事件发射频率 × 每个事件大小  。

默认情况下，时序见解预览版可以在每个时序见解环境中以高达每秒 1 GB (MBps) 的速率引入传入数据。 有基于中心分区的其他限制[](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)。

> [!TIP]
>
> * 请求最多可提供引入速度为 16 MBps 的环境支持。
> * 如果需要更高的吞吐量，请通过 Azure 门户提交支持票证来联系我们。
 
* **示例 1：**

    Contoso Shipping 有 100,000 台设备，每分钟发出事件三次。 事件的大小为 200 字节。 它们使用包含四个分区的 IoT 中心作为时序见解事件源。

    * 时序见解环境的引入速率为：**100,000 台设备 * 200 字节/事件 *（3/60 事件/秒）= 1 MBps**.
    * 每个分区的引入速率为 0.25 MBps。
    * Contoso Shipping 的引入速率不会超过预览规模限制。

* **示例 2：**

    Contoso Fleet Analytics 有 60,000 台设备，每秒发出一个事件。 它们使用分区计数为 4 的事件中心作为时序见解事件源。 事件的大小为 200 字节。

    * 环境引入速率为：**60,000 台设备 * 200 字节/事件 * 1 事件/秒 = 12 MBps**.
    * 每个分区的速率为 3 MBps。
    * Contoso Fleet Analytics 的引入速率超出了环境和分区限制。 他们可以通过 Azure 门户将请求提交到时序见解，以提高其环境的引入速率，并创建具有更多分区的事件中心，使其不超过预览版限制。

#### <a name="hub-partitions-and-per-partition-limits"></a>中心分区和每个分区的限制

规划时序见解环境时，请务必考虑要连接到时序见解的事件源的配置。 Azure IoT 中心和事件中心都利用分区来实现事件处理的水平缩放。 

分区是中心内保留的有序事件。 分区计数是在中心创建阶段设置的，无法更改。

如需了解事件中心分区最佳做法，请参阅[我需要多少分区？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 大多数用于 Azure 时序见解的 IoT 中心只需要四个分区。

无论是为时序见解环境创建新的中心还是使用现有的中心，都需要计算每个分区引入速率，以确定其是否在预览版限制内。 

Azure 时序见解预览版当前的常规每分区限制为 0.5 MBps。

#### <a name="iot-hub-specific-considerations"></a>特定于 IoT 中心的注意事项

在 IoT 中心内创建设备时，该设备将被永久分配给一个分区。 这样，IoT 中心就可以保证事件顺序（因为分配绝不会更改）。

固定分区分配还会影响时序见解实例，这些实例引入从 IoT 中心向下游发送的数据。 当使用相同的网关设备 ID 将来自多个设备的消息转发到中心时，它们可能会在同一时间到达同一分区，这可能会超出每个分区的规模限制。

**影响**：

* 如果单个分区的引入速率持续超过预览版限制，则时序见解可能不会在超过 IoT 中心数据保持期之前同步所有设备遥测数据。 因此，如果持续超过引入限制，则发送的数据可能会丢失。

为了缓解这种情况，我们建议采用以下最佳做法：

* 在部署解决方案之前，计算按环境和分区的引入速率。
* 确保 IoT 中心设备的负载平衡最大。

> [!IMPORTANT]
> 对于使用 IoT 中心作为事件源的环境，请使用正在使用的中心设备数计算引入速率，以确保速率不超过预览版中每个分区限制，即 0.5 MBps。
>
> * 即使若干事件同时到达，也不会超出预览版限制。

  ![IoT 中心分区关系图](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要详细了解如何优化中心吞吐量和分区，请参阅以下资源：

* [IoT 中心规模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中心规模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中心分区](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>数据存储

创建时序见解预览版即用即付 (PAYG) SKU 环境时，需要创建两个 Azure 资源：

* 可为 warm 数据存储配置的 Azure 时序见解预览环境。
* 用于 cold 数据存储的 Azure 存储常规用途 V1 blob 帐户。

warm 存储中的数据只能通过[时序查询](./time-series-insights-update-tsq.md)和 [Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)提供。 warm 存储将包含在创建时序见解环境时选择的[保留期](./time-series-insights-update-plan.md#the-preview-environment)内的最新数据。

时序见解预览版以 [Parquet 文件格式](#parquet-file-format-and-folder-structure)将 cold 存储数据保存到 Azure Blob 存储。 时序见解预览版以特有的方式管理此类 cold 存储数据，但你可以直接将其作为标准 Parquet 文件读取。

> [!WARNING]
> 作为 cold 存储数据所在的 Azure Blob 存储帐户的所有者，你对该帐户中的所有数据具有完全访问权限。 此访问权限包括“写入”和“删除”权限。 请勿编辑或删除时序见解预览版写入的数据，因为这可能会导致数据丢失。

### <a name="data-availability"></a>数据可用性

Azure 时序见解预览版对数据进行分区并编制索引，以实现最佳查询性能。 为数据编制索引后，可以从 warm（如果启用）和 cold 存储查询数据。 正在引入的数据量可能会影响此可用性。

> [!IMPORTANT]
> 在预览期间，可能 60 秒后，数据才可用。 如果出现超过 60 秒的明显延迟，请通过 Azure 门户提交支持票证。

## <a name="azure-storage"></a>Azure 存储

本部分介绍 Azure 时序见解预览版相关的 Azure 存储详细信息。

有关 Azure Blob 存储的全面说明，请参阅[存储 blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>你的存储帐户

创建 Azure 时序见解预览版 PAYG 环境时，Azure 存储常规用途 V1 blob 帐户会创建为长期 cold 存储。  

Azure 时序见解预览版在 Azure 存储帐户中保留每个事件的最多两个副本。 一个副本存储按引入时间排序的事件，始终允许按照按时间顺序访问事件。 随着时间的推移，时序见解预览版还会创建数据的重新分区副本，以针对性能时序见解查询进行优化。

在公共预览版中，数据无限期存储在 Azure 存储帐户中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>编写和编辑时序见解 Blob

为了确保查询性能和数据可用性，请不要编辑或删除时序见解预览版创建的任何 blob。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>访问时序见解预览版 cold 存储数据

除了从[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)和[时序查询](./time-series-insights-update-tsq.md)访问数据外，还可以直接从 cold 存储中存储的 Parquet 文件访问数据。 例如，可以在 Jupyter 笔记本中读取、转换和清理数据，并使用它来训练同一 Spark 工作流中的 Azure 机器学习模型。

若要直接从 Azure 存储帐户访问数据，需要具有用于存储时序见解预览版数据的帐户的读取访问权限。 然后，可以基于以下 [Parquet 文件格式](#parquet-file-format-and-folder-structure)部分中所述的 `PT=Time` 文件夹中的 Parquet 文件的创建时间读取所选数据。  如需详细了解如何启用对存储帐户的读取访问权限，请参阅[管理对存储帐户资源的访问权限](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>数据删除

请勿删除时序见解预览版文件。 仅在时序见解预览版中管理相关数据。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源列式文件格式，用于实现高效存储和性能。 时序见解预览版使用 Parquet 来大规模实现基于时间序列 ID 的查询性能。  

有关 Parquet 文件类型的详细信息，请参阅 [Parquet 文档](https://parquet.apache.org/documentation/latest/)。

时序见解预览版按如下方式存储数据的副本：

* 首先，初始副本按引入时间进行分区，并按到达顺序存储数据。 此数据位于 `PT=Time` 文件夹中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 其次，重新分区副本按时序 ID 分组，并位于 `PT=TsId` 文件夹中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在这两种情况下，Parquet 文件的 time 属性都对应于 blob 创建时间。 在将 `PT=Time` 文件夹中的数据写入文件后，不会对其进行任何更改。 `PT=TsId` 文件夹中的数据将针对一段时间的查询进行优化，而不是静态的。

> [!NOTE]
>
> * `<YYYY>` 映射到 4 位数的年份表示形式。
> * `<MM>` 映射到 2 位数的月份表示形式。
> * `<YYYYMMDDHHMMSSfff>` 映射到时间戳表示形式，该表示形式包含 4 位数的年 (`YYYY`)、2 位数的月 (`MM`)、2 位数的日 (`DD`)、2 位数的小时 (`HH`)、2 位数的分钟(`MM`)、2 位数的秒 (`SS`) 和 3 位数的毫秒 (`fff`)。

时序见解预览版事件按如下所示映射到 Parquet 文件内容：

* 每个事件映射到单个行。
* 每一行都包含时间戳列（具有事件时间戳）。 “时间戳”属性永远不会为 null。 如果未在事件源中指定时间戳属性，该属性默认为“事件排队时间”。 存储的时间戳始终采用 UTC 格式。
* 每一行都包含创建时序见解环境时定义的时序 ID (TSID) 列。 TSID 属性名称包括 `_string` 后缀。
* 作为遥测数据发送的所有其他属性均映射到结尾是 `_string`（字符串）、`_bool`（布尔值）、`_datetime`（日期时间）或 `_double`（双精度值）的列名称，具体取决于属性类型。
* 此映射架构适用于文件格式的第一个版本，作为 V=1 引用并存储在具有相同名称的基文件夹中。 随着此功能的演变，此映射架构可能会更改，并且引用名称将递增。

## <a name="next-steps"></a>后续步骤

- 请参阅[如何为入口和查询调整 JSON ](./time-series-insights-update-how-to-shape-events.md)。

- 了解新型[数据建模](./time-series-insights-update-tsm.md)。
