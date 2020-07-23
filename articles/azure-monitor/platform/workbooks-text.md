---
title: Azure Monitor 工作簿文本参数
description: 使用预生成和自定义的参数化工作簿简化复杂的报表。 详细了解工作簿文本参数。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687330"
---
# <a name="workbook-text-parameters"></a>工作簿文本参数

使用文本框参数可以方便地从工作簿用户那里收集文本输入。 无法使用下拉列表收集输入（例如任意阈值或常规筛选器）时，可以使用文本框参数。 工作簿允许作者从查询中获取文本框的默认值。 这样，就可以实现一些有意义的方案，例如，根据指标的 p95 设置默认阈值。

文本框的常见用途是可以充当其他工作簿控件使用的内部变量。 若要起到这种效果，可以利用查询来获取默认值，并使输入控件在读取模式下隐藏。 例如，用户可能希望某个阈值来自公式（而不是来自用户），然后在后续查询中使用该阈值。

## <a name="creating-a-text-parameter"></a>创建文本参数
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`SlowRequestThreshold`
    2. 参数类型：`Text`
    3. 必需：`checked`
    4. 从查询中获取默认值：`unchecked`
5. 从工具栏中选择“保存”以创建参数。

    ![显示如何创建文本参数的插图](./media/workbooks-text/text-create.png)

这就是工作簿在读取模式下的外观。

![显示处于读取模式的文本参数的插图](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>引用文本参数
1. 选择蓝色的 `Add query` 链接将查询控件添加到工作簿，然后选择 Application Insights 资源。
2. 在 KQL 框中添加以下片段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 结合查询控件使用值为 500 的文本参数有效运行以下查询：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 运行查询以查看结果

    ![显示 KQL 中引用的文本参数的插图](./media/workbooks-text/text-reference.png)

> [!NOTE]
> 在以上示例中，`{SlowRequestThreshold}` 表示一个整数值。 如果查询类似于 `{ComputerName}` 的字符串，则需要修改 Kusto 查询以添加引号 `"{ComputerName}"`，使参数字段接受不带引号的输入。

## <a name="setting-default-values"></a>设置默认值
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`SlowRequestThreshold`
    2. 参数类型：`Text`
    3. 必需：`checked`
    4. 从查询中获取默认值：`checked`
5. 在 KQL 框中添加以下片段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查询将文本框的默认值设置为应用中所有请求的持续时间的 95%。
6. 运行查询以查看结果
7. 从工具栏中选择“保存”以创建参数。

    ![显示包含 KQL 中的默认值的文本参数的插图](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 尽管此示例查询的是 Application Insights 数据，但该方法可用于任何基于日志的数据源 - Log Analytics、Azure Resource Graph 等。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
