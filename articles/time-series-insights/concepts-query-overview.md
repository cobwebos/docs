---
title: 查询数据 - Azure 时序见解第 2 代 | Microsoft Docs
description: Azure 时序见解第 2 代中的数据查询概念和 REST API 概述。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0fb50e01e86c00ac863352118eb36f7470251435
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489147"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>查询来自 Azure 时序见解第 2 代的数据

使用 Azure 时序见解第 2 代可以通过公共 Surface API 对存储在环境中的事件和元数据进行数据查询。 [Azure 时序见解 TSI 资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)也使用这些 api。

Azure 时序见解第 2 代中提供三个主要 API 类别：

* **环境 API**：这些 API 允许查询 Azure 时序见解第 2 代环境本身。 这些 API 可用于收集调用方有权访问的环境列表和环境元数据。
* **时序模型-查询 (TSM-Q) API**：用于针对环境的时序模型中存储的元数据执行创建、读取、更新和删除 (CRUD) 操作。 这些 API 可用于访问和编辑实例、类型与层次结构。
* **时序查询 (TSQ) API**：用于检索从源提供程序记录的遥测数据或事件数据，以及通过高级标量和聚合函数对数据执行高性能计算和聚合。

Azure 时序见解第 2 代使用丰富的基于字符串的表达式语言[时序表达式 (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 来表达[时序变量](./concepts-variables.md)中的计算。

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure 时序见解第 2 代 API 概述

支持以下核心 API。

[![时序查询概述](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>环境 API

* [获取环境 API](https://docs.microsoft.com/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment)：返回调用方有权访问的环境的列表。
* [获取环境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability)：返回事件时间戳 `$ts` 中事件计数的分布。 此 API 通过返回按时间间隔划分的事件计数（如果有），来帮助确定环境中是否出现了任何事件。
* [获取事件架构 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema)：返回给定搜索范围的事件架构元数据。 此 API 可帮助检索给定搜索范围的架构中可用的所有元数据和属性。

## <a name="time-series-model-query-tsm-q-apis"></a>时序模型-查询 (TSM-Q) API

其中的大多数 API 支持批量执行操作，可用于对多个时序模型实体执行批量 CRUD 操作：

* [模型设置 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis)：允许对环境的默认类型和模型名称执行 *GET* 和 *PATCH* 操作。
* [类型 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api)：允许对时序类型及其关联变量执行 CRUD。
* [层次结构 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api)：允许对时序层次结构及其关联的字段路径执行 CRUD。
* [实例 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api)：允许对时序实例及其关联的实例字段执行 CRUD。 另外，实例 API 支持以下操作：
  * [搜索](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)：检索在搜索基于实例属性的时序实例时获得的结果的部分列表。
  * [建议](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)：搜索并建议在搜索基于实例属性的时序实例时获得的结果的部分列表。

## <a name="time-series-query-tsq-apis"></a>时序查询 (TSQ) API

这些 API 对多层存储解决方案中的两种存储（暖存储和冷存储）都可用。 查询 URL 参数用于指定查询应该在其上执行的[存储类型](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)：

* [获取事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)：用于查询和检索原始事件和关联的事件时间戳（当它们从源提供程序记录在 Azure 时序见解第 2 代中时）。 此 API 可用于从给定时序 ID 和搜索范围中检索原始事件。 此 API 支持分页，可以检索选定输入的完整响应数据集。

  > [!IMPORTANT]

  > * 根据 [JSON 平展和转义规则即将发生的变化](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)中的说明，数组将会被存储为动态类型。 若要访问存储为这种类型的有效负载属性，只能通过获取事件 API。

* [获取时序 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)：此 API 对原始事件应用变量定义的计算，可用于查询和检索计算值与关联的事件时间戳。 这些变量可以在时序模型中定义，或者在查询中以内联方式提供。 此 API 支持分页，可以检索选定输入的完整响应数据集。

* [聚合时序 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)：此 API 对原始事件应用变量定义的计算，可用于查询和检索聚合值与关联的间隔时间戳。 这些变量可以在时序模型中定义，或者在查询中以内联方式提供。 此 API 支持分页，可以检索选定输入的完整响应数据集。
  
  对于指定的搜索范围和间隔，此 API 将根据每个间隔和变量返回时序 ID 的聚合响应。 响应数据集中的间隔数的计算方式是，统计纪元计时周期数（自 Unix 纪元 1970 年 1 月 1 日开始消逝的毫秒数），然后将计时周期数除以查询中指定的间隔跨度大小。

  响应集中返回的时间戳与左间隔边界相关，而与间隔中的采样事件无关。

## <a name="next-steps"></a>后续步骤

* 详细了解可在[时序模型](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)中定义的不同变量。
* 详细了解如何从 [Azure 时序见解资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)查询数据。
