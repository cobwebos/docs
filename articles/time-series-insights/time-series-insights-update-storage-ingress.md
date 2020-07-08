---
title: 预览版中的数据存储和引入 - Azure 时序见解 | Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据存储和引入。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: d3bfb589ec4c152b136e8e1f432864b719c97d58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509313"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据存储和入口

本文介绍 Azure 时序见解预览版中对数据存储和入口的更新。 其中介绍了底层存储结构、文件格式和时序 ID 属性。 此外，还介绍了底层引入过程、最佳做法和当前的预览版限制。

## <a name="data-ingress"></a>数据入口

Azure 时序见解环境包含一个用于收集、处理和存储时序数据的引入引擎。

[规划环境](time-series-insights-update-plan.md)时，需要考虑一些事项，以确保处理所有传入的数据，实现较高的引入规模，并最大限度地降低引入延迟（时序见解读取和处理事件源中的数据所花费的时间）。

在 Azure 时序见解预览版中，数据引入策略决定了数据可以源自何处，以及应采用哪种格式。

### <a name="ingress-policies"></a>入口策略

数据引入涉及到如何将数据发送到 Azure 时序见解预览版环境。

下面汇总了关键配置、格式和最佳做法。

#### <a name="event-sources"></a>事件源

Azure 时序见解预览版支持以下事件源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

Azure 时序见解预览版针对每个实例最多支持两个事件源。 连接事件源时，TSI 环境会从最早的事件开始，读取当前存储在 IoT 中心或事件中心的所有事件。

> [!IMPORTANT]
>
> * 将事件源附加到预览环境时，可能会发生较高的初始延迟。
> 事件源的延迟取决于 IoT 中心或事件中心内当前的事件数。
> * 在事件源数据第一次引入后，高延迟会降低。 如果持续遇到较高的延迟，请通过 Azure 门户提交支持票证。

#### <a name="supported-data-format-and-types"></a>支持的数据格式和类型

Azure 时序见解支持通过 Azure IoT 中心或 Azure 事件中心发送的 UTF-8 编码 JSON。 

支持的数据类型为：

| 数据类型 | 说明 |
|---|---|
| **bool** | 具有两种状态之一的数据类型：`true` 或 `false`。 |
| **dateTime** | 表示时间上的一刻，通常以日期和当天的时间表示。 以 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 格式表示。 |
| **long** | 已签名的64位整数  |
| **double** | 双精度 64 位 [IEEE 754](https://ieeexplore.ieee.org/document/8766229) 浮点数。 |
| **string** | 文本值，包含 Unicode 字符。          |

> [!IMPORTANT]
>
> * 你的 TSI 环境为强类型。 如果设备或标记同时发送整型和非整型数据，则设备属性值将存储在两个分隔的 double 列中，并且在进行 API 调用和定义时序模型变量表达式时，应使用[联合（）函数](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

#### <a name="objects-and-arrays"></a>对象和数组

可将复杂类型（例如对象和数组）作为事件有效负载的一部分发送，但存储的数据将会经历一个平展过程。

[如何调整要引入和查询的 JSON](./time-series-insights-update-how-to-shape-events.md) 中提供了有关如何调整 JSON 事件、发送复杂类型和嵌套对象平展的详细信息，以帮助你进行规划和优化。

### <a name="ingress-best-practices"></a>入口最佳做法

建议使用以下最佳做法：

* 在同一区域中配置 Azure 时序见解和任何 IoT 中心或事件中心，以降低潜在的延迟。

* 通过计算预计引入速率并检查计算结果是否处于下面所列的受支持速率范围内，来[规划规模需求](time-series-insights-update-plan.md)。

* 阅读[如何为入口和查询调整 JSON](./time-series-insights-update-how-to-shape-events.md)，了解如何优化和调整 JSON 数据以及预览版的当前限制。

* 流式传输引入仅限用于近实时数据和最新数据，不支持流式传输历史数据。

#### <a name="historical-data-ingestion"></a>历史数据引入

Azure 时序见解预览版目前不支持使用流式传输管道导入历史数据。 如果需要将过去的数据导入到环境中，请遵循以下准则：

* 不要并行传输实时和历史数据。 引入无序数据将导致查询性能下降。
* 按时间顺序引入历史数据，以获得最佳性能。
* 不超过以下引入吞吐率上限。
* 如果数据早于你的 Warm 存储保留期，请禁用 Warm 存储。

### <a name="ingress-scale-and-preview-limitations"></a>入口规模和预览版限制

Azure 时序见解预览版的引入限制如下所述。

> [!TIP]
> 有关所有预览版限制的综合列表，请阅读[规划预览版环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)。

#### <a name="per-environment-limitations"></a>基于环境的限制

一般而言，引入速率被视为与组织中的设备数、事件发出频率以及每个事件的大小因素有关：

*  设备数 × 事件发射频率 × 每个事件大小  。

默认情况下，时序见解预览版可以在每个时序见解环境中以高达每秒 1 GB (MBps) 的速率引入传入数据。 存在针对[单个中心分区](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)的其他限制。

> [!TIP]
>
> * 请求最多可提供引入速度为 16 MBps 的环境支持。
> * 如果需要更高的吞吐量，请通过在 Azure 门户中提交支持票证来联系我们。
 
* **示例 1：**

    Contoso Shipping 有 100,000 台设备，每分钟发出事件三次。 事件的大小为 200 字节。 它们使用包含四个分区的 IoT 中心作为时序见解事件源。

    * 时序见解环境的引入速率为：**100,000 个设备 * 200 字节/事件 * (每秒 3 个事件/60) = 1 MBps**。
    * 每个分区的引入速率为 0.25 MBps。
    * Contoso Shipping 的引入速率不会超过预览规模限制。

* **示例 2：**

    Contoso Fleet Analytics 有 60,000 台设备，每秒发出一个事件。 Contoso Fleet Analytics 使用分区计数为 4 的事件中心作为时序见解事件源。 事件的大小为 200 字节。

    * 环境引入速率为：60,000 设备 * 200 字节/事件 * 1 事件/秒 = 12 MBps。
    * 每个分区的速率为 3 MBps。
    * Contoso Fleet Analytics 的引入速率超出了环境和分区限制。 它们可以通过 Azure 门户向时序见解提交一个请求，要求提高其环境的引入速率，并创建一个事件中心，提高预览版限制中的分区数。

#### <a name="hub-partitions-and-per-partition-limits"></a>中心分区和每个分区的限制

规划时序见解环境时，必须考虑要连接到时序见解的事件源的配置。 Azure IoT 中心和事件中心都利用分区来实现事件处理的水平缩放。 

分区是中心内保留的有序事件。 分区计数是在中心创建阶段设置的，且不可更改。

有关事件中心分区的最佳做法，请参阅[我需要多少分区？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 与 Azure 时序见解配合使用的大多数 IoT 中心只需要 4 个分区。

无论是为时序见解环境创建新的中心还是使用现有的中心，都需要计算每个分区的引入速率，以确定它是否在预览版的限制范围内。 

在 Azure 时序见解预览版中，**每个分区的限制目前为 0.5 MBps**。

#### <a name="iot-hub-specific-considerations"></a>特定于 IoT 中心的注意事项

在 IoT 中心内创建设备时，会将该设备永久分配到某个分区。 这样，IoT 中心就可以保证事件的排序（因为分配永远不会更改）。

固定的分区分配也会影响引入下游 IoT 中心发送的数据的时序见解实例。 使用相同的网关设备 ID 将来自多个设备的消息转发到中心时，这些消息可能抵达同一分区，同时，可能会超出每个分区的规模限制。

**影响**：

* 如果单个分区的引入速率持续超过预览版的限制，则时序见解在超出 IoT 中心数据保留期之前可能不会同步所有设备遥测数据。 因此，如果持续超出引入限制，发送的数据可能会丢失。

为了缓解这种情况，我们建议采用以下最佳做法：

* 在部署解决方案之前按环境和按分区引入速率进行计算。
* 确保以最大可能的程度对 IoT 中心设备进行负载均衡。

> [!IMPORTANT]
> 对于使用 IoT 中心作为事件源的环境，请使用正在使用的中心设备数计算引入速率，以确保速率不超过预览版中每个分区限制，即 0.5 MBps。
>
> * 即使多个事件同时抵达，也不会超出预览版的限制。

  ![IoT 中心分区关系图](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要详细了解如何优化中心吞吐量和分区，请参阅以下资源：

* [IoT 中心规模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中心规模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中心分区](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>数据存储

创建时序见解预览版即用即付 (PAYG) SKU 环境时，需要创建两个 Azure 资源：

* 可为暖数据存储配置的 Azure 时序见解预览版环境。
* 用于 cold 数据存储的 Azure 存储常规用途 V1 blob 帐户。

warm 存储中的数据只能通过[时序查询](./time-series-insights-update-tsq.md)和 [Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)提供。 warm 存储将包含在创建时序见解环境时选择的[保留期](./time-series-insights-update-plan.md#the-preview-environment)内的最新数据。

时序见解预览版以 [Parquet 文件格式](#parquet-file-format-and-folder-structure)将 cold 存储数据保存到 Azure Blob 存储。 时序见解预览版以独占方式管理此冷存储数据，但允许将这些数据作为标准 Parquet 文件直接进行读取。

> [!WARNING]
> 冷存储数据所在 Azure Blob 存储帐户的所有者对该帐户中的所有数据拥有完全访问权限。 此访问权限包括“写入”和“删除”权限。 请不要编辑或删除时序见解预览版写入的数据，否则可能导致数据丢失。

### <a name="data-availability"></a>数据可用性

Azure 时序见解预览版对数据进行分区并编制索引，以实现最佳查询性能。 为数据编制索引后，就可以从热存储（如果已启用）和冷存储中查询数据。 正在引入的数据量可能会影响此可用性。

> [!IMPORTANT]
> 在预览版中，可能需要等待最长 60 秒才能看到数据。 如果出现超过 60 秒的明显延迟，请通过 Azure 门户提交支持票证。

## <a name="azure-storage"></a>Azure 存储

本部分介绍与 Azure 时序见解预览版相关的 Azure 存储详细信息。

有关 Azure Blob 存储的全面介绍，请阅读[存储 Blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>你的存储帐户

创建 Azure 时序见解预览版 PAYG 环境时，Azure 存储常规用途 V1 blob 帐户会创建为长期 cold 存储。  

Azure 时序见解预览版在 Azure 存储帐户中为每个事件保留最多两个副本。 一个副本存储按引入时间排序的事件，始终允许按时序访问事件。 随着时间的推移，时序见解预览版还会创建数据的重新分区的副本，通过优化实现高性能时序见解查询。

在公共预览版中，数据无限期存储在 Azure 存储帐户中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>编写和编辑时序见解 Blob

为了确保查询性能和数据可用性，请不要编辑或删除时序见解预览版创建的任何 blob。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>访问时序见解预览版 cold 存储数据

除了从[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)和[时序查询](./time-series-insights-update-tsq.md)访问数据外，还可以直接从 cold 存储中存储的 Parquet 文件访问数据。 例如，可以在 Jupyter 笔记本中读取、转换和清理数据，并使用它来训练同一 Spark 工作流中的 Azure 机器学习模型。

若要直接从 Azure 存储帐户访问数据，需要具有用于存储时序见解预览版数据的帐户的读取访问权限。 然后，可以根据 Parquet 文件的创建时间读取选定的数据，该文件位于下面的 [Parquet 文件格式](#parquet-file-format-and-folder-structure)部分所述的 `PT=Time` 文件夹中。  若要详细了解如何启用对存储帐户的读取访问权限，请参阅[管理对存储帐户资源的访问权限](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>数据删除

请勿删除时序见解预览版文件。 请只在时序见解预览版内部管理相关数据。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源的列式文件格式，旨在提高存储效率和性能。 时序见解预览版使用 Parquet 来大规模启用基于时序 ID 的查询的性能。  

有关 Parquet 文件类型的详细信息，请参阅 [Parquet 文档](https://parquet.apache.org/documentation/latest/)。

时序见解预览版按如下方式存储数据的副本：

* 首先，初始副本按引入时间进行分区，并按到达顺序存储数据。 该数据驻留在 `PT=Time` 文件夹中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 其次，重新分区的副本按时序 ID 分组，驻留在 `PT=TsId` 文件夹中：

  `V=1/PT=TsId/<TSI_INTERNAL_STRUCTURE>/<TSI_INTERNAL_NAME>.parquet`

文件夹中的 blob 名称的时间戳 `PT=Time` 对应于数据到 TSI （而不是事件的时间戳）的到达时间。

`PT=TsId` 文件夹中的数据会不断针对查询进行优化，不是静态的。 在重新分区过程中，同一事件可能出现在多个 blob 中。 此外，blob 的命名以后可能会更改。

> [!NOTE]
>
> * `<YYYY>` 映射到 4 位数的年份表示形式。
> * `<MM>` 映射到两位数的月份表示形式。
> * `<YYYYMMDDHHMMSSfff>` 映射到时间戳表示形式，其中包括四位数的年份 (`YYYY`)、两位数的月份 (`MM`)、两位数的日 (`DD`)、两位数的小时 (`HH`)、两位数的分钟 (`MM`)、两位数的秒 (`SS`) 和三位数的毫秒 (`fff`)。

时序见解预览版事件按如下所示映射到 Parquet 文件内容：

* 每个事件映射到单个行。
* 每一行包含带有事件时间戳的**时间戳**列。 “时间戳”属性永远不会为 null。 如果未在事件源中指定时间戳属性，该属性默认为**事件排队时间**。 存储的时间戳始终为 UTC 时间。
* 每一行包含创建时序见解环境时定义的时序 ID (TSID) 列。 TSID 属性名称包含 `_string` 后缀。
* 作为遥测数据发送的所有其他属性均映射到结尾是 `_string`（字符串）、`_bool`（布尔值）、`_datetime`（日期时间）或 `_double`（双精度值）的列名称，具体取决于属性类型。
* 此映射架构适用于文件格式的第一个版本（以 **V=1** 表示，存储在使用相同名称的基文件夹中）。 随着此功能的演变，此映射架构可能会更改，引用名称的数字会递增。

## <a name="next-steps"></a>后续步骤

- 阅读[如何针对传入和查询调整 JSON](./time-series-insights-update-how-to-shape-events.md)。

- 了解新型[数据建模](./time-series-insights-update-tsm.md)。
