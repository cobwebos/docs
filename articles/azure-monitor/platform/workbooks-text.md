---
title: Azure Monitor 工作簿文本参数
description: 利用预生成的自定义参数化工作簿，简化复杂的报告工作。 了解有关工作簿文本参数的详细信息。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658043"
---
# <a name="workbook-text-parameters"></a>工作簿文本参数

Textbox 参数提供了一种简单的方法来从工作簿用户那里收集文本输入。 当不可行地使用下拉项收集输入（例如，任意阈值或一般筛选器）时，使用它们。 工作簿允许作者从查询中获取 textbox 的默认值。 这样就可以根据指标的 p95 设置默认阈值，这种情况很有趣。

文本框的常见用途是作为其他工作簿控件使用的内部变量。 这是通过对默认值进行查询，并使输入控件在读模式下不可见来实现的。 例如，用户可能希望阈值来自公式（而非用户），然后在后续查询中使用该阈值。

## <a name="creating-a-text-parameter"></a>创建文本参数
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `SlowRequestThreshold`
    2. 参数类型： `Text`
    3. 必需： `checked`
    4. 从查询中获取默认值： `unchecked`
5. 从工具栏中选择 "保存"，创建参数。

    ![显示文本参数创建的图像](./media/workbooks-text/text-create.png)

这就是工作簿在读取模式下的外观。

![显示处于读取模式的文本参数的图像](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>引用文本参数
1. 通过选择蓝色 `Add query` 链接向工作簿添加查询控件并选择 Application Insights 资源。
2. 在 KQL 框中，添加以下代码片段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 通过将 text 参数的值500和查询控件结合使用来有效地运行以下查询：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 运行查询以查看结果

    ![显示 KQL 中引用的文本参数的图像](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>设置默认值
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `SlowRequestThreshold`
    2. 参数类型： `Text`
    3. 必需： `checked`
    4. 从查询中获取默认值： `checked`
5. 在 KQL 框中，添加以下代码片段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查询将文本框的默认值设置为应用程序中所有请求的95% 的持续时间。
6. 运行查询以查看结果
7. 从工具栏中选择 "保存"，创建参数。

    ![显示来自 KQL 的默认值的文本参数的图像](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 尽管此示例查询 Application Insights 数据，但该方法可用于基于日志的任何数据源-Log Analytics、Azure 资源图等。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
