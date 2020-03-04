---
title: 预览中的数据存储和入口-Azure 时序见解 |Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据存储和入口。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246184"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据存储和入口

本文介绍适用于 Azure 时序见解预览的数据存储和入口更新。 其中介绍了基础存储结构、文件格式和时序 ID 属性。 还介绍了底层入口流程、最佳做法和当前预览限制。

## <a name="data-ingress"></a>数据入口

你的 Azure 时序见解环境包含一个*引入引擎*，用于收集、处理和存储时序数据。 

在[规划环境](time-series-insights-update-plan.md)时，需要注意一些注意事项，以确保处理所有传入数据，实现高进入规模，并最大限度地减少*引入延迟*（时序见解从事件来源读取和处理数据所用的时间）。

时序见解预览数据入口策略确定数据的来源位置，以及数据应采用的格式。

### <a name="ingress-policies"></a>入口策略

*数据*引入涉及到将数据发送到 Azure 时序见解预览环境的方式。 

下面总结了密钥配置、格式和最佳实践。

#### <a name="event-sources"></a>事件源

Azure 时序见解预览版支持以下事件源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

Azure 时序见解预览版每个实例最多支持两个事件源。

> [!IMPORTANT] 
> * 将事件源附加到预览环境时，可能会遇到较高的初始延迟。 
> 事件源延迟取决于当前在 IoT 中心或事件中心内的事件数。
> * 在事件源数据第一次引入后，会下降高延迟。 如果遇到高延迟，请通过 Azure 门户提交支持票证。

#### <a name="supported-data-format-and-types"></a>支持的数据格式和类型

Azure 时序见解支持从 Azure IoT 中心或 Azure 事件中心发送的 UTF-8 编码的 JSON。 

支持的数据类型包括：

| 数据类型 | 说明 |
|---|---|
| **bool** | 具有以下两种状态之一的数据类型： `true` 或 `false`。 |
| **dateTime** | 表示时间上的一刻，通常以日期和当天的时间表示。 用[ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)格式表示。 |
| **double** | 双精度64位[IEEE 754](https://ieeexplore.ieee.org/document/8766229)浮点。 |
| **string** | 文本值，包含 Unicode 字符。          |

#### <a name="objects-and-arrays"></a>对象和数组

您可以将复杂类型（如对象和数组）作为事件有效负载的一部分发送，但在存储数据时，您的数据将经历一个平展过程。 

[如何将 json 用于入口和查询](./time-series-insights-update-how-to-shape-events.md)，以帮助进行规划和优化，详细介绍了如何调整 json 事件、发送复杂类型和嵌套对象平展的详细信息。

### <a name="ingress-best-practices"></a>入口最佳实践

建议使用以下最佳做法：

* 在同一区域中配置 Azure 时序见解和任何 IoT 中心或事件中心，以减少潜在的延迟。

* 通过计算预计引入率并验证其是否处于下面列出的支持费率中来[规划规模需求](time-series-insights-update-plan.md)。

* 通过阅读[如何将 json 用于入口和查询](./time-series-insights-update-how-to-shape-events.md)，了解如何优化和调整 json 数据以及预览版的当前限制。

### <a name="ingress-scale-and-preview-limitations"></a>入口规模和预览限制 

Azure 时序见解预览入口限制如下所述。

> [!TIP]
> 阅读[规划预览环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)以获取所有预览限制的综合列表。

#### <a name="per-environment-limitations"></a>每个环境限制

通常，会将入口速率视为你的组织中的设备数、事件发射频率和每个事件的大小的系数：

*  **设备数**×**事件发射频率**×**每个事件的大小**。

默认情况下，时序见解预览可以按**每秒的速率（MBps）每个时序见解环境**传入传入数据。

> [!TIP] 
> * 请求最多可提供引入速度为 16 MBps 的环境支持。
> * 如果需要更高的吞吐量，请通过 Azure 门户提交支持票证来联系我们。
 
* **示例 1：**

    Contoso 发货包含100000台设备，每分钟发出一次事件三次。 事件的大小为200个字节。 它们使用包含四个分区作为时序见解事件源的事件中心。

    * 其时序见解环境的引入速率为： **100000 设备 * 200 字节/事件 * （3/60 事件/秒） = 1 MBps**。
    * 每个分区的引入速率为 0.25 MBps。
    * Contoso 发货的引入率将在预览规模限制范围内。

* **示例2：**

    Contoso 汽油分析具有60000个设备，每秒发出一个事件。 它们将 IoT 中心24分区计数4作为时序见解事件源。 事件的大小为200个字节。

    * 环境引入速率为： **20000 设备 * 200 字节/事件 * 1 事件/秒 = 4 MBps**。
    * 每个分区速率为 1 MBps。
    * Contoso 汽油分析可以通过 Azure 门户向时序见解提交请求，以增加其环境的引入速率。

#### <a name="hub-partitions-and-per-partition-limits"></a>中心分区和每个分区的限制

规划时序见解环境时，必须考虑要连接到时序见解的事件源的配置，这一点很重要。 Azure IoT 中心和事件中心都利用分区来实现事件处理的水平缩放。 

*分区*是中心内保留事件的有序序列。 分区计数是在中心创建阶段设置的，无法更改。 

对于事件中心分区最佳实践，请查看[需要多少分区？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 大多数使用 Azure 时序见解的 IoT 中心只需四个分区。

无论是为时序见解环境创建新的中心还是使用现有的集线器，都需要按分区引入速率来计算，以确定其是否在预览限制内。 

Azure 时序见解预览版当前的**每个分区限制为 0.5 MBps**。

#### <a name="iot-hub-specific-considerations"></a>特定于 IoT 中心的注意事项

在 IoT 中心内创建设备时，该设备将被永久分配给分区。 这样，IoT 中心就可以保证事件顺序（因为赋值从不会更改）。

固定分区分配还会影响时序见解实例，这些实例引入 IoT 中心发送的数据。 当使用相同的网关设备 ID 将来自多个设备的消息转发到集线器时，它们可能会在同一时间到达同一分区，可能会超出每个分区的规模限制。 

**影响**：

* 如果单个分区在预览限制的情况下出现持续速率，则时序见解可能不会在已超出 IoT 中心数据保持期之前同步所有设备遥测数据。 因此，如果引入限制始终超过，则发送的数据可能会丢失。

为了缓解这种情况，我们建议采用以下最佳做法：

* 在部署解决方案之前，按环境和分区引入速率计算。
* 确保 IoT 中心设备的负载平衡最大。

> [!IMPORTANT]
> 对于使用 IoT 中心作为事件源的环境，请使用正在使用的集线器设备数计算引入速率，以确保在预览版中，每个分区限制的速率低于 0.5 MBps。
> * 即使多个事件同时到达，也不会超出预览限制。

  ![IoT 中心分区关系图](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要详细了解如何优化集线器吞吐量和分区，请参阅以下资源：

* [IoT 中心规模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中心规模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中心分区](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>数据存储

创建时序见解预览*即用即付*（PAYG） SKU 环境时，需要创建两个 Azure 资源：

* 可为热数据存储配置的 Azure 时序见解预览环境。
* 用于冷数据存储的 Azure 存储常规用途 V1 blob 帐户。

你的热存储中的数据仅可通过[时序查询](./time-series-insights-update-tsq.md)和[Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)使用。 你的温存储将包含创建时序见解环境时选择的[保持期](./time-series-insights-update-plan.md#the-preview-environment)内的最新数据。

时序见解预览版以[Parquet 文件格式](#parquet-file-format-and-folder-structure)将冷存储数据保存到 Azure Blob 存储。 时序见解预览版以独占方式管理此冷存储数据，但可以直接作为标准 Parquet 文件读取。

> [!WARNING]
> 作为冷存储数据所在的 Azure Blob 存储帐户的所有者，你对该帐户中的所有数据具有完全访问权限。 此访问权限包括 "写入" 和 "删除" 权限。 请勿编辑或删除时序见解预览写入的数据，因为这可能会导致数据丢失。

### <a name="data-availability"></a>数据可用性

Azure 时序见解预览分区和索引数据以获得最佳查询性能。 为数据编制索引后，可以从热（如果启用）和冷存储进行查询。 正在引入的数据量可能会影响此可用性。

> [!IMPORTANT]
> 在预览期间，你可能会遇到长达60秒的时间，然后数据才可用。 如果遇到超过60秒的明显延迟，请通过 Azure 门户提交支持票证。

## <a name="azure-storage"></a>Azure 存储

本部分介绍 azure 时序见解预览版中的 Azure 存储空间详细信息。

有关 Azure Blob 存储的全面说明，请参阅[存储 blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>你的存储帐户

创建 Azure 时序见解预览版 PAYG 环境时，会将 Azure 存储空间常规用途 V1 blob 帐户创建为长期冷存储。  

Azure 时序见解预览版在你的 Azure 存储帐户中保留每个事件的两个副本。 一个副本存储按引入时间排序的事件，始终允许按照按时间顺序访问事件。 随着时间的推移，时序见解预览还会创建数据的重新分区副本，以针对性能时序见解查询进行优化。 

公共预览期间，数据将无限期地存储在 Azure 存储帐户中。

#### <a name="writing-and-editing-time-series-insights-blobs"></a>编写和编辑时序见解 Blob

若要确保查询性能和数据可用性，请不要编辑或删除时序见解预览创建的任何 blob。

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>访问时序见解预览版冷存储数据 

除了从时序[见解预览资源管理器](./time-series-insights-update-explorer.md)和[时序查询](./time-series-insights-update-tsq.md)访问数据外，还可能需要直接从存储在 cold 存储区中的 Parquet 文件访问数据。 例如，可以在 Jupyter 笔记本中读取、转换和清理数据，并使用它来训练同一 Spark 工作流中的 Azure 机器学习模型。

若要直接从 Azure 存储帐户访问数据，需要具有用于存储时序见解预览数据的帐户的读取访问权限。 然后，可以根据 Parquet 文件的创建时间，在[Parquet 文件格式](#parquet-file-format-and-folder-structure)部分中描述的 `PT=Time` 文件夹中读取选定的数据。  有关启用对存储帐户的读取访问权限的详细信息，请参阅[管理对你的存储帐户资源的访问权限](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>数据删除

请勿删除时序见解预览文件。 仅在时序见解预览版中管理相关数据。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源纵栏文件格式，旨在提高存储和性能。 时序见解预览版使用 Parquet 来大规模启用基于时间序列 ID 的查询性能。  

有关 Parquet 文件类型的详细信息，请参阅[Parquet 文档](https://parquet.apache.org/documentation/latest/)。

时序见解预览版按如下方式存储数据的副本：

* 首先，初始副本按引入时间进行分区，并按到达顺序存储数据。 此数据位于 `PT=Time` 文件夹中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 其次，重新分区副本按时序 Id 分组，并驻留在 `PT=TsId` 文件夹中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在这两种情况下，Parquet 文件的 time 属性都对应于 blob 创建时间。 在将 `PT=Time` 文件夹中的数据写入文件后，不会对其进行任何更改。 `PT=TsId` 文件夹中的数据将针对一段时间的查询进行优化，而不是静态的。

> [!NOTE]
> * `<YYYY>` 映射为四位数年份表示形式。
> * `<MM>` 映射到两位数的月份表示形式。
> * `<YYYYMMDDHHMMSSfff>` 映射到具有四位数年份（`YYYY`）、两位数月份（`MM`）、两位数日期（`DD`）、两位数小时（`HH`）、两位数分钟（`MM`）、两位数秒（`SS`）和三位数毫秒（`fff`）的时间戳表示形式。

时序见解预览版事件映射到 Parquet 文件内容，如下所示：

* 每个事件映射到单个行。
* 每一行都包含带有事件时间戳的**timestamp**列。 时间戳属性决不会为 null。 如果事件源中未指定时间戳属性，则默认为**事件排队时间**。 存储的时间戳始终采用 UTC 格式。
* 每一行都包含创建时序见解环境时定义的时序 ID （TSID）列。 TSID 属性名称包括 `_string` 后缀。
* 作为遥测数据发送的所有其他属性都映射到以 `_string` （string）、`_bool` （布尔）、`_datetime` （datetime）或 `_double` （double）结尾的列名称，具体取决于属性类型。
* 此映射架构适用于文件格式的第一个版本，作为**V = 1**引用并存储在具有相同名称的基文件夹中。 随着此功能的演变，此映射架构可能会更改，并且引用名称将递增。

## <a name="next-steps"></a>后续步骤

- 阅读[如何将 JSON 作为入口和 query 的形状](./time-series-insights-update-how-to-shape-events.md)。

- 了解新的[数据建模](./time-series-insights-update-tsm.md)。
