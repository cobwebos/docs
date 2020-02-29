---
title: Azure 数据资源管理器中的时序异常检测 & 预测
description: 了解如何使用 Azure 数据资源管理器分析时序数据的异常情况检测和预测。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194151"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Azure 数据资源管理器中的异常检测和预测

Azure 数据资源管理器会从云服务或 IoT 设备对遥测数据进行日常收集。 此数据是针对各种见解进行分析的，如监视服务运行状况、物理生产过程、使用趋势和负载预测。 分析是在所选指标的时序上完成的，以查找指标相对于其典型普通基线模式的偏差模式。 Azure 数据资源管理器包含对多个时序的创建、处理和分析的本机支持。 它可以在数秒内创建和分析数千个时间序列，实现近乎实时的监视解决方案和工作流。

本文详细介绍 Azure 数据资源管理器时序异常检测和预测功能。 适用的时序函数基于强大的众所周知分解模型，其中每个原始时序分解为季节性、趋势和残留组件。 异常通过残留组件上的离群值检测，而预测是通过推断季节性和趋势组件来完成的。 Azure 数据资源管理器实现通过自动季节性检测、强健的离群分析和向量化实现（以秒为单位）处理上千个时间序列，大大增强了基本分解模型。

## <a name="prerequisites"></a>必备条件

有关时序功能的概述，请参阅[Azure 中的时序分析数据资源管理器](/azure/data-explorer/time-series-analysis)。

## <a name="time-series-decomposition-model"></a>时序分解模型

Azure 数据资源管理器时序预测和异常情况检测的本机实现使用众所周知的分解模型。 此模型适用于预计预定定期和趋势行为（如服务流量、组件检测信号和 IoT 定期度量值）以预测未来指标值和检测异常值的时序指标。 此回归过程的假设是除了先前已知的季节性和趋势行为以外，还会随机分发时序。 然后，你可以从季节性和趋势组件（统称为基线）预测未来指标值，并忽略剩余部分。 你还可以使用仅残留部分来检测不是基于离群值分析的异常值。
若要创建分解模型，请使用函数[`series_decompose()`](/azure/kusto/query/series-decomposefunction)。 `series_decompose()` 函数采用一组时序，并自动分解每个时序到其季节性、趋势、残留和基线组件。 

例如，可以使用以下查询来分解内部 web 服务的流量：

**\[** [**单击以运行查询**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![时序分解](media/anomaly-detection/series-decompose-timechart.png)

* 原始时序标记为**num** （红色）。 
* 该过程通过使用函数[`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction)自动检测季节性并提取**季节性**模式（紫色）开始。
* 将从原始时序中减去季节性模式，并使用函数[`series_fit_line()`](/azure/kusto/query/series-fit-linefunction)运行 "线性回归" 来查找**趋势**组件（浅蓝色）。
* 函数减去趋势，余数为**剩余**分量（绿色）。
* 最后，该函数添加了季节性和趋势组件来生成**基线**（蓝色）。

## <a name="time-series-anomaly-detection"></a>时序异常检测

函数[`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction)查找一组时序中的异常点。 此函数将调用 `series_decompose()` 来生成分解模型，然后在残留组件上运行[`series_outliers()`](/azure/kusto/query/series-outliersfunction) 。 `series_outliers()` 使用 Tukey 的围栏测试来计算残留组件每个点的异常分数。 高于1.5 或低于1.5 的异常分数表示有轻度异常上升或下降。 异常分数高于3.0 或低于3.0，表示存在强异常。 

以下查询允许检测内部 web 服务流量中的异常：

**\[** [**单击以运行查询**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![时序异常检测](media/anomaly-detection/series-anomaly-detection.png)

* 原始时序（以红色表示）。 
* 基线（季节性 + 趋势）组件（蓝色）。
* 原始时序顶部的异常点（紫色）。 异常点明显偏离了预期的基线值。

## <a name="time-series-forecasting"></a>时序预测

函数[`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction)预测一组时序的未来值。 此函数将调用 `series_decompose()` 来生成分解模型，然后针对每个时序将基线组件推断到将来。

以下查询允许预测下周的 web 服务流量：

**\[** [**单击以运行查询**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![时序预测](media/anomaly-detection/series-forecasting.png)

* 原始指标（红色）。 默认情况下，缺少未来值并将其设置为0。
* 外推基线组件（蓝色）以预测下周的值。

## <a name="scalability"></a>可伸缩性

Azure 数据资源管理器查询语言语法允许单个调用处理多个时序。 它的独特优化实现允许快速性能，这对于在接近实时的情况下监视上千个计数器时的有效异常检测和预测至关重要。

下面的查询显示三个时间序列的同时处理：

**\[** [**单击以运行查询**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![时序伸缩性](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>总结

本文档详细介绍了用于时序异常检测和预测的本机 Azure 数据资源管理器功能。 每个原始时序都分解为季节性、趋势和残留组件来检测异常和/或预测。 这些功能可用于接近实时的监视方案，如故障检测、预测性维护以及需求和负载预测。

## <a name="next-steps"></a>后续步骤

了解 Azure 数据资源管理器中的[机器学习功能](/azure/data-explorer/machine-learning-clustering)。
