---
title: Azure 时序见解预览版中的数据存储和入口 | Microsoft Docs
description: 了解 Azure 时序见解预览版中的数据存储和入口。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989706"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的数据存储和入口

本文介绍适用于 Azure 时序见解预览的数据存储和入口更新。 其中包括底层存储结构、文件格式和时序 ID 属性。 它还讨论了底层入口过程、吞吐量和限制。

## <a name="data-ingress"></a>数据入口

在时序见解预览中，数据入口策略确定数据的来源位置以及数据应采用的格式。

[![时序模型概述](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>入口策略

时序见解预览版支持时序见解当前支持的相同事件源：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

时序见解预览版每个实例最多支持两个事件源。
  
Azure 时序见解支持通过 Azure IoT 中心或 Azure 事件中心提交的 JSON。 若要优化 IoT JSON 数据，请了解[如何为 json](./time-series-insights-send-events.md#supported-json-shapes)。

### <a name="data-storage"></a>数据存储

创建时序见解预览即用即付 SKU 环境时，需要创建两个 Azure 资源：

* 时序见解预览环境，可以选择包括热存储功能。
* 用于冷数据存储的 Azure 存储常规用途 V1 blob 帐户。

你的热存储中的数据仅可通过[时序查询](./time-series-insights-update-tsq.md)和[Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)使用。 

时序见解预览版以[Parquet 文件格式](#parquet-file-format-and-folder-structure)将冷存储数据保存到 Azure Blob 存储。 时序见解预览版以独占方式管理此冷存储数据，但可以直接作为标准 Parquet 文件读取。

> [!WARNING]
> 作为冷存储数据所在的 Azure Blob 存储帐户的所有者，你对该帐户中的所有数据具有完全访问权限。 此访问权限包括 "写入" 和 "删除" 权限。 请勿编辑或删除时序见解预览写入的数据，因为这可能会导致数据丢失。

### <a name="data-availability"></a>数据可用性
时序见解预览分区和索引数据以获得最佳查询性能。 索引数据后，可对其进行查询。 正在引入的数据量可能会影响此可用性。

> [!IMPORTANT]
> 时序见解的正式发布（GA）版本将使数据在从事件源读取后的60秒内可用。 预览期间，你可能会遇到较长的一段时间，然后数据才会可用。 如果遇到超过60秒的明显延迟，请联系我们。

### <a name="scale"></a>调整规模

默认情况下，时序见解预览版支持每秒最大为 1 mb/秒（MB/秒）的每个环境的初始入口。 如果需要，最高可达 16 MB/秒的吞吐量。 如果需要增强的缩放支持，请联系我们。

可以获取事件源的其他入口和缩放功能：

* [IoT 中心](../iot-hub/iot-hub-scaling.md)
* [事件中心](../event-hubs/event-hubs-scalability.md)

## <a name="azure-storage"></a>Azure 存储器

本部分介绍 azure 时序见解预览版中的 Azure 存储空间详细信息。

有关 Azure Blob 存储的全面说明，请参阅[存储 blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>你的存储帐户

创建时序见解预览即用即付环境时，会将 Azure 存储空间常规用途 V1 blob 帐户创建为长期冷存储。  

时序见解预览版在你的 Azure 存储帐户中发布每个事件的两个副本。 初始副本包含按引入时间排序的事件，并且始终保留这些事件，因此你可以使用其他服务来访问它。 你可以使用 Spark、Hadoop 和其他熟悉的工具来处理原始 Parquet 文件。 

时序见解预览版对为时序见解查询进行优化的 Parquet 文件。 还会保存此数据的重新分区副本。

公共预览期间，数据将无限期地存储在 Azure 存储帐户中。

### <a name="writing-and-editing-time-series-insights-blobs"></a>编写和编辑时序见解 Blob

若要确保查询性能和数据可用性，请不要编辑或删除时序见解预览创建的任何 blob。

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>从时序见解预览版访问和导出数据

你可能希望访问在时序见解预览资源管理器中查看的数据，以便与其他服务一起使用。 例如，你可以使用数据在 Power BI 中生成报表，或使用 Azure 机器学习 Studio 来训练机器学习模型。 或者，你可以使用你的数据在 Jupyter 笔记本中进行转换、可视化和建模。

可通过三种常规方式访问数据：

* 通过时序见解预览版资源管理器。 你可以从资源管理器中将数据导出为 CSV 文件。 有关详细信息，请参阅[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。
* 来自时序见解预览 API。 可以在 `/getRecorded`访问 API 终结点。 有关此 API 的详细信息，请参阅[时序查询](./time-series-insights-update-tsq.md)。
* 从 Azure 存储帐户直接访问。 需要对用于访问时序见解预览数据的任何帐户进行读取访问。 有关详细信息，请参阅[管理对存储帐户资源的访问权限](../storage/blobs/storage-manage-access-to-resources.md)。

### <a name="data-deletion"></a>数据删除

请勿删除时序见解预览文件。 只应在时序见解预览版中管理相关数据。

## <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源纵栏文件格式，旨在实现高效的存储和性能。 由于这些原因，时序见解预览版使用 Parquet。 它按时间序列 ID 对数据进行分区，以便大规模查询性能。  

有关 Parquet 文件类型的详细信息，请参阅[Parquet 文档](https://parquet.apache.org/documentation/latest/)。

时序见解预览版按如下方式存储数据的副本：

* 首先，初始副本按引入时间进行分区，并按到达顺序存储数据。 数据驻留在 `PT=Time` 文件夹中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 其次，重新分区副本按一组时序 Id 进行分区，并驻留在 `PT=TsId` 文件夹中：

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

在这两种情况下，时间值都对应于 blob 创建时间。 保留 `PT=Time` 文件夹中的数据。 `PT=TsId` 文件夹中的数据将针对一段时间的查询进行优化，而不会保持静态。

> [!NOTE]
> * `<YYYY>` 映射为四位数年份表示形式。
> * `<MM>` 映射到两位数的月份表示形式。
> * `<YYYYMMDDHHMMSSfff>` 映射到具有四位数年份（`YYYY`）、两位数月（`MM`）、两位数日期（`DD`）、两位数小时（`HH`）、两位数分钟（`MM`）、两位数秒（`SS`）和三位数毫秒（`fff`）。

时序见解预览版事件映射到 Parquet 文件内容，如下所示：

* 每个事件映射到单个行。
* 每一行都包含带有事件时间戳的**timestamp**列。 时间戳属性决不会为 null。 如果事件源中未指定时间戳属性，则默认为**事件排队时间**。 时间戳始终采用 UTC 格式。
* 每一行都包含创建时序见解环境时定义的时序 ID 列。 属性名称包括 `_string` 后缀。
* 作为遥测数据发送的所有其他属性都映射到以 `_string` （string）、`_bool` （布尔）、`_datetime` （datetime）或 `_double` （double）结尾的列名称，具体取决于属性类型。
* 此映射方案适用于文件格式的第一个版本，作为**V = 1**引用。 随着此功能的演变，名称可能会增加。

## <a name="next-steps"></a>后续步骤

- 阅读[Azure 时序见解预览版存储和入口](./time-series-insights-update-storage-ingress.md)。

- 了解新的[数据建模](./time-series-insights-update-tsm.md)。
