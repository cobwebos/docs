---
title: Azure Monitor 工作簿时间参数
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 1d1ee243fa9df8a77a6ce80ecb9d8e5336e8b19b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872751"
---
# <a name="workbook-time-parameters"></a>工作簿时间参数

时间参数允许用户设置分析的时间上下文，并供几乎所有报表使用。 进行设置和使用相对简单，允许作者指定要在下拉范围中显示的时间范围，包括自定义时间范围的选项。 

## <a name="creating-a-time-parameter"></a>创建时间参数
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `TimeRange`
    2. 参数类型： `Time range picker`
    3. 必需： `checked`
    4. 可用时间范围：过去小时、过去12小时、过去24小时、过去48小时、过去3天、过去7天和允许自定义时间范围选择
5. 从工具栏中选择 "保存"，创建参数。

    ![显示时间范围参数创建的图像](./media/workbooks-time/time-settings.png)

这就是工作簿在读取模式下的外观。

![在读取模式下显示时间范围参数的图像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>引用时间参数
### <a name="via-bindings"></a>Via 绑定
1. 向工作簿添加查询控件并选择 Application Insights 资源。
2. 大多数工作簿控件支持_时间范围_范围选择器。 打开 "_时间范围_" 下拉箭头，然后在底部的 "排名参数" 组中选择 "`{TimeRange}`"。
3. 这会将时间范围参数绑定到图表的时间范围。 现在，示例查询的时间范围是过去24小时。
4. 运行查询以查看结果

    ![显示通过绑定引用的时间范围参数的图像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 向工作簿添加查询控件并选择 Application Insights 资源。
2. 在 KQL 中，输入使用参数的时间范围筛选器： `| where timestamp {TimeRange}`
3. 这会将查询计算时间扩大到 `| where timestamp > ago(1d)`，这是参数的时间范围值。
4. 运行查询以查看结果

    ![显示 KQL 中引用的时间范围的图像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>文本中 
1. 向工作簿添加文本控件。
2. 在 markdown 中，输入 `The chosen time range is {TimeRange:label}`
3. 选择 "_完成编辑_"
4. 文本控件将显示文本：所_选时间范围是过去24小时_

## <a name="time-parameter-options"></a>时间参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 时间范围标签 | 过去 24 小时 |
| `{TimeRange:label}` | 时间范围标签 | 过去 24 小时 |
| `{TimeRange:value}` | 时间范围值 | > 前（1d） |
| `{TimeRange:query}` | 时间范围查询 | > 前（1d） |
| `{TimeRange:start}` | 时间范围开始时间 | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | 时间范围结束时间 | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | 时间范围粒度 | 30 m |


### <a name="using-parameter-options-in-a-query"></a>在查询中使用参数选项
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
