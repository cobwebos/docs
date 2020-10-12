---
title: Azure Monitor 工作簿时间参数
description: 了解如何设置时间参数以允许用户设置分析的时间上下文。 几乎所有报表都使用时间参数。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c84133d5728f7b6593e0606dda2eef28b8167e43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90972870"
---
# <a name="workbook-time-parameters"></a>工作簿时间参数

时间参数使用户可以设置分析的时间上下文，几乎所有的报表都会使用它。 时间参数的设置和使用相对简单 - 允许作者指定要在下拉列表中显示的时间范围，包括自定义时间范围的选项。 

## <a name="creating-a-time-parameter"></a>创建时间参数
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`TimeRange`
    2. 参数类型：`Time range picker`
    3. 必需：`checked`
    4. 可用时间范围：过去 1 小时、过去 12 小时、过去 24 小时、过去 48 小时、过去 3 天、过去 7 天，以及允许自定义时间范围选择
5. 从工具栏中选择“保存”以创建参数。

    ![显示创建时间范围参数的图像](./media/workbooks-time/time-settings.png)

这就是工作簿在读取模式下的外观。

![在读取模式下显示时间范围参数的图像](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>引用时间参数
### <a name="via-bindings"></a>通过绑定的方式
1. 向工作簿添加查询控件，然后选择 Application Insights 资源。
2. 大多数工作簿控件支持“时间范围”范围选取器。 打开“时间范围”下拉列表，然后选择底部时间范围参数组中的 `{TimeRange}`。
3. 这会将时间范围参数绑定到图表的时间范围。 现在示例查询的时间范围为“过去 24 小时”。
4. 运行查询以查看结果

    ![显示通过绑定引用时间范围参数的图像](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 在工作簿中添加查询控件，然后选择 Application Insights 资源。
2. 在 KQL 中，输入使用以下参数的时间范围筛选器：`| where timestamp {TimeRange}`
3. 这会将查询计算时间扩大到 `| where timestamp > ago(1d)`，这就是参数的时间范围值。
4. 运行查询以查看结果

    ![显示在 KQL 中引用时间范围的图像](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>在文本中 
1. 向工作簿添加文件控件。
2. 在 Markdown 中，输入 `The chosen time range is {TimeRange:label}`
3. 选择“完成编辑”
4. 文本控件将显示文本：所选的时间范围为“过去 24 小时”

## <a name="time-parameter-options"></a>时间参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | 时间范围标签 | 过去 24 小时 |
| `{TimeRange:label}` | 时间范围标签 | 过去 24 小时 |
| `{TimeRange:value}` | 时间范围值 | > ago(1d) |
| `{TimeRange:query}` | 时间范围查询 | > ago(1d) |
| `{TimeRange:start}` | 时间范围开始时间 | 2019/3/20 下午 4:18 |
| `{TimeRange:end}` | 时间范围结束时间 | 2019/3/21 下午 4:18 |
| `{TimeRange:grain}` | 时间范围粒度 | 30 分钟 |


### <a name="using-parameter-options-in-a-query"></a>在查询中使用参数选项
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
