---
title: Azure 时序见解预览版数据查询 | Microsoft Docs
description: Azure 时序见解预览版数据查询。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 97265a83a73d45f45a4bd1183df61521f4ca29bf
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989693"
---
# <a name="data-querying"></a>数据查询

Azure 时序见解预览版允许通过公共 Surface API 对存储在环境中的事件和元数据进行数据查询。 这些 API 也用于[时序见解预览版资源管理器](./time-series-insights-update-explorer.md)。

时序见解中提供三个主要 API 类别：

* **环境 api**：这些 api 允许在时序见解环境本身上进行查询。 查询示例包括调用方有权访问的环境和环境元数据的列表。
* **时序模型-查询（TSM） api**：对存储在时序模型环境部分中的元数据启用创建、读取、更新和删除（CRUD）操作。 示例包括实例、类型和层次结构。
* **时序查询（TSQ） api**：允许检索遥测数据或事件数据，因为它是从源提供程序记录的，或者通过使用标量和聚合函数存储的变量来减少数据。 这些 Api 可以执行操作来转换、组合和应用时序数据的计算。

时序见解使用基于字符串的丰富表达式语言（即时序[表达式（TSX））](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)来表示计算。

## <a name="azure-time-series-insights-preview-core-apis"></a>Azure 时序见解预览版核心 API

支持以下核心 API。

[![时序查询概述](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>环境 API

提供以下环境 API：

* [获取环境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api)：返回调用方有权访问的环境的列表。
* [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api)：返回事件计数在事件时间戳 `$ts` 上的分布。 此 API 通过返回事件计数（如果存在）来帮助确定时间戳中是否有任何事件。
* [获取事件架构 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api)：返回给定搜索范围的事件架构元数据。 此 API 可帮助检索给定搜索范围的架构中可用的所有元数据和属性。

## <a name="time-series-model-query-tsm-q-apis"></a>时序模型-查询 (TSM-Q) API

以下时序模型-查询 Api 可用。 其中的大多数 Api 都支持批处理执行操作，以便对多个时序模型实体启用批处理 CRUD 操作：

* [模型设置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)：对默认类型和环境的型号名称启用*GET*和*PATCH* 。
* [类型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)：对时序类型及其关联变量启用 CRUD。
* [层次结构 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)：在时序层次结构及其关联的字段路径上启用 CRUD。
* [实例 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)：在时序实例及其关联的实例字段上启用 CRUD。 此外，实例 API 还支持下列操作：
  * [搜索](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search)：根据实例属性检索时序实例搜索的部分命中列表。
  * [建议](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest)：根据实例属性，搜索并建议搜索时序实例的部分命中列表。

## <a name="time-series-query-tsq-apis"></a>时序查询 (TSQ) API

提供以下时序查询 Api。 可在时序见解中的所有支持的多层存储上使用这些 Api。 查询 URL 参数用于指定应在其上执行查询的[存储类型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters)：

* [获取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api)：在来自源提供程序的时序见解中记录的事件中，启用对时序见解数据的查询和检索。 此 API 允许检索给定时序 ID 和搜索范围内的原始事件。 此 API 支持分页以检索所选输入的完整数据集。 

* [获取系列 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api)：通过使用网络上记录的数据，从捕获的事件中查询和检索时序见解数据。 返回的值基于模型中定义的变量或以内联方式提供的变量。 此 API 支持分页以检索所选输入的完整数据集。 此 API 有助于定义计算属性或列。

    >[!NOTE]
    > 即使在模型中指定或以内联方式提供 Aggregation 子句，也会忽略该子句。

  获取时序 API 为每个时间间隔的每个变量返回一个时序值。 时序值是时序见解用于从查询输出 JSON 的格式。 返回的值基于所提供的时序 ID 和变量集。

* [聚合序列 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api)：通过对记录的数据进行采样和聚合，启用捕获的事件中的时序见解数据的查询和检索。 此 API 通过使用[继续标记支持持续](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#queryresultpage)执行。

  聚合时序 API 为每个时间间隔的每个变量返回一个时序值。 这些值基于所提供的时序 ID 和变量集。 聚合时序 API 使用存储在时序模型中或以内联方式提供的变量对数据进行聚合或采样，以实现缩减操作。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 时序见解预览版中的[存储和入口](./time-series-insights-update-storage-ingress.md)。
- 阅读时序见解预览[数据建模](./time-series-insights-update-tsm.md)一文。
- 了解[选择时序 ID 的最佳实践](./time-series-insights-update-how-to-id.md)。
