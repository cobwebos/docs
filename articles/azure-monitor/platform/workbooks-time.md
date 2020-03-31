---
title: Azure 监视器工作簿时间参数
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658007"
---
# <a name="workbook-time-parameters"></a>工作簿时间参数

时间参数允许用户设置分析的时间上下文，并且几乎所有报表都使用。 设置和使用相对简单 - 允许作者指定要在下拉列表中显示的时间范围，包括自定义时间范围的选项。 

## <a name="creating-a-time-parameter"></a>创建时间参数
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`TimeRange`
    2. 参数类型：`Time range picker`
    3. 必填：`checked`
    4. 可用时间范围：最后一小时、最后 12 小时、最近 24 小时、最近 48 小时、最近 3 天、最近 7 天以及允许自定义时间范围选择
5. 从工具栏中选择"保存"以创建参数。

    ![显示时间范围参数创建的图像](./media/workbooks-time/time-settings.png)

这就是工作簿在阅读模式下的样子。

![在读取模式下显示时间范围参数的图像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>引用时间参数
### <a name="via-bindings"></a>通过绑定
1. 向工作簿添加查询控件并选择应用程序见解资源。
2. 大多数工作簿控件都支持_时间范围_选取器。 打开_时间范围_下拉列表，`{TimeRange}`并在底部选择时间铃参数组。
3. 这将时间范围参数绑定到图表的时间范围。 示例查询的时间范围现在是最近 24 小时。
4. 运行查询以查看结果

    ![显示通过绑定引用的时间范围参数的图像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 向工作簿添加查询控件并选择应用程序见解资源。
2. 在 KQL 中，使用 参数输入时间范围筛选器：`| where timestamp {TimeRange}`
3. 这将在查询评估时间上扩展为`| where timestamp > ago(1d)`，这是参数的时间范围值。
4. 运行查询以查看结果

    ![显示 KQL 中引用的时间范围的图像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>在文本中 
1. 向工作簿添加文本控件。
2. 在标记下，输入`The chosen time range is {TimeRange:label}`
3. 选择 _"已完成编辑"_
4. 文本控件将显示文本：_所选时间范围为"过去 24 小时"_

## <a name="time-parameter-options"></a>时间参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 时间范围标签 | 过去 24 小时 |
| `{TimeRange:label}` | 时间范围标签 | 过去 24 小时 |
| `{TimeRange:value}` | 时间范围值 | >前（1d） |
| `{TimeRange:query}` | 时间范围查询 | >前（1d） |
| `{TimeRange:start}` | 时间范围开始时间 | 2019/3/20 下午 4：18 |
| `{TimeRange:end}` | 时间范围结束时间 | 2019/3/21 下午 4：18 |
| `{TimeRange:grain}` | 时间范围颗粒 | 30 米 |


### <a name="using-parameter-options-in-a-query"></a>在查询中使用参数选项
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
