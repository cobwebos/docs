---
title: 查询数据-Azure 时序见解 Gen2 |Microsoft Docs
description: Azure 时序见解 Gen2 中的数据查询概念和 REST API 概述。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: abc1f1de45fe846f9f50a6a7aa3dae2ed837b52b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486788"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>查询 Azure 时序见解 Gen2 中的数据

Azure 时序见解 Gen2 通过公共 surface Api 启用对环境中存储的事件和元数据的数据查询。 [Azure 时序见解 Gen2 资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)也使用这些 api。

Azure 时序见解 Gen2 中有三个主要 API 类别：

* **环境 api**：这些 Api 在 Azure 时序见解 Gen2 环境本身上启用查询。 这些可用于收集调用方有权访问的环境的列表和环境元数据。
* **时序模型-查询（TSM） api**：允许对存储在环境时序模型中的元数据的创建、读取、更新和删除（CRUD）操作。 它们可用于访问和编辑实例、类型和层次结构。
* **时序查询（TSQ） api**：允许检索遥测数据或事件数据，因为它是从源提供程序记录的，并使用高级标量和聚合函数对数据启用高性能计算和聚合。

Azure 时序见解 Gen2 使用基于字符串的丰富表达式语言（即时序[表达式（TSX））](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)来表示[时序变量](./concepts-variables.md)中的计算。

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure 时序见解 Gen2 Api 概述

支持以下核心 API。

[![时序查询概述](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>环境 API

* [获取环境 API](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)：返回调用方有权访问的环境的列表。
* [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability)：通过事件时间戳返回事件计数的分布 `$ts` 。 此 API 通过返回被分解为时间间隔（如果有）的事件计数来帮助确定环境中是否存在任何事件。
* [获取事件架构 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)：返回给定搜索范围的事件架构元数据。 此 API 可帮助检索给定搜索范围的架构中可用的所有元数据和属性。

## <a name="time-series-model-query-tsm-q-apis"></a>时序模型-查询 (TSM-Q) API

其中的大多数 Api 都支持批处理执行操作，以便对多个时序模型实体启用批处理 CRUD 操作：

* [模型设置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)：对默认类型和环境的型号名称启用*GET*和*PATCH* 。
* [类型 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api)：对时序类型及其关联变量启用 CRUD。
* [层次结构 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api)：在时序层次结构及其关联的字段路径上启用 CRUD。
* [实例 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api)：在时序实例及其关联的实例字段上启用 CRUD。 另外，实例 API 支持以下操作：
  * [搜索](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)：根据实例属性检索时序实例搜索的部分命中列表。
  * [建议](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)：根据实例属性，搜索并建议搜索时序实例的部分命中列表。

## <a name="time-series-query-tsq-apis"></a>时序查询 (TSQ) API

多层存储解决方案中的两个存储（热和冷）都提供这些 Api。 查询 URL 参数用于指定查询应该在其上执行的[存储类型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)：

* [获取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)：在 Azure 时序见解 Gen2 中记录源提供程序时，启用对原始事件和关联事件时间戳的查询和检索。 此 API 可用于从给定时序 ID 和搜索范围中检索原始事件。 此 API 支持分页以检索所选输入的完整响应数据集。 

  > [!IMPORTANT]
  > * 作为[即将发生的 JSON 平展和转义规则更改](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)的一部分，数组将存储为**动态**类型。 作为此类型存储的负载属性**只能通过获取事件 API 进行访问**。

* [获取系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)：通过应用原始事件上变量定义的计算，启用计算值和关联事件时间戳的查询和检索。 这些变量可以在时序模型中定义，也可以在查询中以内联方式提供。 此 API 支持分页以检索所选输入的完整响应数据集。 

* [聚合序列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)：通过应用原始事件上的变量定义的计算，启用聚合值和关联时间戳的查询和检索。 这些变量可以在时序模型中定义，也可以在查询中以内联方式提供。 此 API 支持分页以检索所选输入的完整响应数据集。 
  
  对于指定的搜索范围和间隔，此 API 为时序 ID 返回每个变量的每个间隔的聚合响应。 响应数据集中的间隔数是通过计算 epoch 刻度（自 Unix epoch-Jan 1，1970以来已经过去的毫秒数）计算出来的，并将刻度除以查询中指定的时间间隔范围大小。

  响应集中返回的时间戳为左侧间隔边界，而不是来自间隔的采样事件。 

## <a name="next-steps"></a>后续步骤

- 阅读有关可在[时序模型](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)中定义的不同变量的详细信息。
- 详细了解如何从[Azure 时序见解 Gen2 资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)查询数据。
