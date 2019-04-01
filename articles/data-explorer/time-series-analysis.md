---
title: Azure 数据资源管理器时序分析
description: '了解 Azure 数据资源管理器中的时序分析 '
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 6a77e399e4de6ec41e74d1e5b9f9f518126958c2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756769"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Azure 数据资源管理器中的时序分析

Azure 数据资源管理器 (ADX) 持续从云服务或 IoT 设备收集遥测数据。 分析这些数据可获得各种见解，例如，监视服务的运行状况、物理生产流程和使用趋势。 分析是针对所选指标的时序执行的，以找出某种模式与其典型基线模式之间的偏差。
ADX 原生支持创建、操作和分析多个时序。 本主题介绍如何使用 ADX 在**几秒钟内创建和分析数千个时序**，实现近实时的监视解决方案和工作流。

## <a name="time-series-creation"></a>时序的创建

在本部分，我们将使用 `make-series` 运算符方便直观地创建大量正则时序，并根据需要填充缺失值。
时序分析的第一个步骤是将原始遥测表分区并转换为一组时序。 该表通常包含时间戳列、上下文维度和可选指标。 维度用于将数据分区。 目标是按固定的时间间隔为每个分区创建数千个时序。

输入表 *demo_make_series1* 包含任意 Web 服务流量的 60 万条记录。 使用以下命令对 10 条记录采样：

```kusto
demo_make_series1 | take 10 
```

生成的表包含一个时间戳列、三个上下文维度列，但不包含指标：

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | TimeStamp | BrowserVer | OsVer | 国家/地区 |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | 英国 |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | 英国 |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | 英国 |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | 立陶宛共和国 |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | 印度 |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | 英国 |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | 荷兰 |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | 英国 |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | 印度 |

由于没有指标，我们只能生成一组时序，用于表示由 OS 使用以下查询分区的流量计数本身：

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- 使用 [`make-series`](/azure/kusto/query/make-seriesoperator) 运算符创建由三个时序组成的集，其中：
    - `num=count()`：流量的时序
    - `range(min_t, max_t, 1h)`：时序是在时间范围（表记录的最旧和最新时间戳）的 1 小时箱中创建的
    - `default=0`：指定缺失箱的填充方法，以创建正则时序。 或者，使用 [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction)、[`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction)、[`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) 和 [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) 进行更改
    - `byOsVer`：由 OS 分区
- 实际时序数据结构是每个时间箱的聚合值的数值数组。 我们将使用 `render timechart` 进行可视化。

上表包含三个分区。 如下图所示，我们可为每个 OS 版本创建不同的时序：Windows 10（红色）、7（蓝色）和 8.1（绿色）：

![时序分区](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>时序分析函数

在本部分，我们将执行典型的时序处理函数。
创建一组时序后，ADX 支持使用[时序文档](/azure/kusto/query/machine-learning-and-tsa)中所述的一系列函数（该列表不断扩充）来处理和分析这些时序。 下面将介绍几个用于处理和分析时序的有代表性函数。

### <a name="filtering"></a>筛选

在信号处理中，筛选是常见的活动，可用于完成时序处理任务（例如，平滑化干扰信号、变化检测）。
- 有两个泛型筛选函数：
    - [`series_fir()`](/azure/kusto/query/series-firfunction)：应用 FIR 筛选器。 用于方便计算变化检测中时序的移动平均值和差异。
    - [`series_iir()`](/azure/kusto/query/series-iirfunction)：应用 IIR 筛选器。 用于指数平滑与累计求和。
- 通过将大小为 5 个箱的新移动平均时序（名为 *ma_num*）添加到查询，来 `Extend`（扩展）时序集：

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![时序筛选](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>回归分析

ADX 支持使用分段线性回归分析来评估时序的趋势。
- 使用 [series_fit_line()](/azure/kusto/query/series-fit-linefunction) 将最佳线条拟合到时序即可实现一般趋势检测。
- 使用 [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) 可以检测相对于基线的趋势变化，这种变化在监视方案中非常有用。

时序查询中 `series_fit_line()` 和 `series_fit_2lines()` 函数的示例：

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![时序回归](media/time-series-analysis/time-series-regression.png)

- 蓝色：原始时序
- 绿色：拟合的线条
- 红色：两条拟合的线条

> [!NOTE]
> 该函数准确检测到了跳接（级别变化）点。

### <a name="seasonality-detection"></a>季节性检测

许多指标遵循季节性（周期）模式。 云服务的用户流量通常包含每日和每周模式，在工作日的大约中段时间，流量是最高的；在夜间和周末，流量是最低的。 IoT 传感器按固定的间隔测量指标。 温度、压力或湿度等物理测量值也可能呈现季节性的行为。

以下示例针对 Web 服务的一个月流量（2 小时箱）应用季节性检测：

```kusto
demo_series3
| render timechart 
```

![时序季节性](media/time-series-analysis/time-series-seasonality.png)

- 使用 [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) 自动检测时序中的周期。 
- 如果知道某个指标应有特定的非重复周期，并且想要验证这些周期是否存在，则使用 [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction)。

> [!NOTE]
> 如果特定的非重复周期不存在，则表示出现异常

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mvexpand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | periods | 评分 | days |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 第 |

该函数会检测每日和每周季节性。 每日评分小于每周评分，因为周末的天数不同于工作日天数。

### <a name="element-wise-functions"></a>元素对应的函数

可针对时序执行算术和逻辑运算。 使用 [series_subtract()](/azure/kusto/query/series-subtractfunction) 可以计算残差时序（原始指标与平滑化指标之差），并查看剩留信号中的异常：

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![时序运算](media/time-series-analysis/time-series-operations.png)

- 蓝色：原始时序
- 红色：经过平滑处理的时序
- 绿色：剩余的时序

## <a name="time-series-workflow-at-scale"></a>时序的大规模工作流

以下示例演示如何在几秒钟内针对数千个时序大规模运行这些函数，以进行异常情况检测。 若要查看 DB 服务的读取计数指标在过去四天的几个示例遥测记录，请运行以下查询：

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

和简单的统计信息：

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

在读取指标的 1 小时箱中生成时序（总共 4 天 * 24 小时 = 96 个点）会产生正态模式波动：

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![大规模时序](media/time-series-analysis/time-series-at-scale.png)

上述行为有误导性，因为单个正态时序是从数千个可能具有异常模式的不同实例聚合而成的。 因此，我们需为每个实例创建一个时序。 实例由 Loc（位置）、anonOp（运算）和 DB（特定计算机）定义。

可以创建多少个时序？

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Count |
|   | 18339 |

现在，我们将创建由读取计数指标的 18339 个时序组成的集。 将 `by` 子句添加到 make-series 语句，应用线性回归，并选择具有最明显递减趋势的前两个时序：

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![前两个时序](media/time-series-analysis/time-series-top-2.png)

显示实例：

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | Op | DB | slope |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

在不到两分钟的时间内，ADX 分析了接近 20,000 个时序并检测到了读取计数骤然下降的两个异常时序。

将这些高级功能与 ADX 的高速性能相结合，可为时序分析提供独特且强大的解决方案。
