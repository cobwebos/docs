---
title: Azure 监视器工作簿文本参数
description: 使用预构建和自定义参数化工作簿简化复杂报告。 详细了解工作簿文本参数。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658043"
---
# <a name="workbook-text-parameters"></a>工作簿文本参数

文本框参数提供了一种从工作簿用户收集文本输入的简单方法。 当使用下拉列表来收集输入（例如，任意阈值或泛型筛选器）时，使用这些输入。 工作簿允许作者从查询中获取文本框的默认值。 这允许有趣的方案，如根据指标的 p95 设置默认阈值。

文本框的常见用途是其他工作簿控件使用的内部变量。 这是通过利用默认值的查询，并使输入控件在读取模式下不可见来实现的。 例如，用户可能希望阈值来自公式（而不是用户），然后在后续查询中使用阈值。

## <a name="creating-a-text-parameter"></a>创建文本参数
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`SlowRequestThreshold`
    2. 参数类型：`Text`
    3. 必填：`checked`
    4. 从查询获取默认值：`unchecked`
5. 从工具栏中选择"保存"以创建参数。

    ![显示文本参数创建的图像](./media/workbooks-text/text-create.png)

这就是工作簿在阅读模式下的样子。

![在读取模式下显示文本参数的图像](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>引用文本参数
1. 通过选择蓝色`Add query`链接并选择"应用程序见解"资源，将查询控件添加到工作簿。
2. 在 KQL 框中，添加以下代码段：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. 通过使用值为 500 的文本参数与查询控件结合使用，您可以有效地运行下面的查询：
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. 运行查询以查看结果

    ![显示 KQL 中引用的文本参数的图像](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>设置默认值
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`SlowRequestThreshold`
    2. 参数类型：`Text`
    3. 必填：`checked`
    4. 从查询获取默认值：`checked`
5. 在 KQL 框中，添加以下代码段：
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    此查询将文本框的默认值设置为应用中所有请求的第 95 个百分位持续时间。
6. 运行查询以查看结果
7. 从工具栏中选择"保存"以创建参数。

    ![显示具有 KQL 默认值的文本参数的图像](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> 此示例查询应用程序见解数据时，该方法可用于任何基于日志的数据源 - 日志分析、Azure 资源图等。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
