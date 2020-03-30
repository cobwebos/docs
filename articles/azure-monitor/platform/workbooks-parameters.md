---
title: Azure 监视器工作簿创建参数
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658211"
---
# <a name="workbook-parameters"></a>工作簿参数

参数允许工作簿作者从使用者那里收集输入，并将其引用到工作簿的其他部分中 - 通常用于限定结果集或设置正确的视觉对象。 它是一项关键功能，允许作者构建交互式报告和体验。 

工作簿允许您控制如何向使用者显示参数控件 - 文本框与下拉、单选与多选、文本值、JSON、KQL 或 Azure 资源图等。  

支持的参数类型包括：
* [时间](workbooks-time.md)- 允许用户从预填充的时间范围中选择或选择自定义范围
* [下拉](workbooks-dropdowns.md)- 允许用户从值或一组值中选择
* [文本](workbooks-text.md)- 允许用户输入任意文本
* [资源](workbooks-resources.md)- 允许用户选择一个或多个 Azure 资源
* [订阅](workbooks-resources.md)- 允许用户选择一个或多个 Azure 订阅资源
* 资源类型 - 允许用户选择一个或多个 Azure 资源类型值
* 位置 - 允许用户选择一个或多个 Azure 位置值

这些参数值可以通过绑定或值扩展在工作簿的其他部分中引用。

## <a name="creating-a-parameter"></a>创建参数
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称`TimeRange`*：（请注意参数__名称__**不能**包含空格或特殊字符）*
    2. 显示名称`Time Range`  *：（但是，__显示名称__可以包括空格、特殊字符、表情符号等）*
    2. 参数类型：`Time range picker`
    3. 必填：`checked`
    4. 可用时间范围：最后一小时、最后 12 小时、最近 24 小时、最近 48 小时、最近 3 天、最近 7 天以及允许自定义时间范围选择
5. 从工具栏中选择"保存"以创建参数。

   ![显示时间范围参数创建的图像](./media/workbooks-parameters/time-settings.png)

这就是工作簿在阅读模式下的样子，在"Pills"样式中。

   ![在读取模式下显示时间范围参数的图像](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>引用参数
### <a name="via-bindings"></a>通过绑定
1. 向工作簿添加查询控件并选择应用程序见解资源。
2. 打开_时间范围_下拉，并从底部的`Time Range`"参数"部分选择该选项。
3. 这将时间范围参数绑定到图表的时间范围。 示例查询的时间范围现在是最近 24 小时。
4. 运行查询以查看结果

    ![显示通过绑定引用的时间范围参数的图像](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 向工作簿添加查询控件并选择应用程序见解资源。
2. 在 KQL 中，使用 参数输入时间范围筛选器：`| where timestamp {TimeRange}`
3. 这将在查询评估时间上扩展为`| where timestamp > ago(1d)`，这是参数的时间范围值。
4. 运行查询以查看结果

    ![显示 KQL 中引用的时间范围的图像](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>在文本中 
1. 向工作簿添加文本控件。
2. 在标记下，输入`The chosen time range is {TimeRange:label}`
3. 选择 _"已完成编辑"_
4. 文本控件将显示文本：_所选时间范围为"过去 24 小时"_

## <a name="parameter-options"></a>参数选项
_"输入文本"_ 部分使用`label`参数 而不是其值。 参数根据其类型公开各种此类选项 - 例如时间范围选取器允许值、标签、查询、开始、结束和颗粒。

使用`Previews`_"编辑参数"_ 窗格的部分查看参数的扩展选项：

![显示时间范围参数选项的图像](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
