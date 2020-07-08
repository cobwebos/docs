---
title: Azure Monitor 工作簿 - 创建参数
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658211"
---
# <a name="workbook-parameters"></a>工作簿参数

使用参数，工作簿作者可以收集来自使用者的输入并在工作簿的其他部分中引用该输入，通常用于确定结果集的范围或设置正确的视觉对象。 参数是一项重要功能，可让作者生成交互式报表和体验。 

使用工作簿，可以控制如何将参数控件呈现给使用者–文本框与下拉框、从文本、JSON、KQL 或 Azure 资源图中选择的值，等等。  

支持的参数类型包括：
* [时间](workbooks-time.md) - 可让用户从预填充的时间范围中进行选择，或选择自定义范围
* [下拉列表](workbooks-dropdowns.md) - 可让用户从一个值或一组值中进行选择
* [文本](workbooks-text.md) - 可让用户输入任意文本
* [资源](workbooks-resources.md) - 可让用户选择一个或多个 Azure 资源
* [订阅](workbooks-resources.md) - 可让用户选择一个或多个 Azure 订阅资源
* 资源类型 - 可让用户选择一个或多个 Azure 资源类型值
* 位置 - 可让用户选择一个或多个 Azure 位置值

可以通过绑定或值扩展在工作簿的其他部分引用这些参数值。

## <a name="creating-a-parameter"></a>创建参数
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`TimeRange`（请注意，参数__名称__**不能**包含空格或特殊字符）
    2. 显示名称：`Time Range`（但是，__显示名称__可以包括空格、特殊字符、表情符号等）
    2. 参数类型：`Time range picker`
    3. 必需：`checked`
    4. 可用时间范围：过去 1 小时、过去 12 小时、过去 24 小时、过去 48 小时、过去 3 天、过去 7 天，以及允许自定义时间范围选择
5. 从工具栏中选择“保存”以创建参数。

   ![显示创建时间范围参数的图像](./media/workbooks-parameters/time-settings.png)

这就是工作簿在读取模式下的“胶囊”式外观。

   ![在读取模式下显示时间范围参数的图像](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>引用参数
### <a name="via-bindings"></a>通过绑定的方式
1. 在工作簿中添加查询控件，然后选择 Application Insights 资源。
2. 打开“时间范围”下拉列表，然后从底部的“参数”部分选择 `Time Range` 选项。
3. 这会将时间范围参数绑定到图表的时间范围。 现在示例查询的时间范围为“过去 24 小时”。
4. 运行查询以查看结果

    ![显示通过绑定引用时间范围参数的图像](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>在 KQL 中
1. 在工作簿中添加查询控件，然后选择 Application Insights 资源。
2. 在 KQL 中，输入使用以下参数的时间范围筛选器：`| where timestamp {TimeRange}`
3. 这会将查询计算时间扩大到 `| where timestamp > ago(1d)`，这就是参数的时间范围值。
4. 运行查询以查看结果

    ![显示在 KQL 中引用时间范围的图像](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>在文本中 
1. 向工作簿添加文件控件。
2. 在 Markdown 中，输入 `The chosen time range is {TimeRange:label}`
3. 选择“完成编辑”
4. 文本控件将显示文本：所选的时间范围为“过去 24 小时”

## <a name="parameter-options"></a>参数选项
“在文本中”部分使用了参数的 `label` 而不是其值。 参数会根据其类型公开各种此类选项 - 例如，时间范围选取器允许值、标签、查询、开始时间、结束时间和粒度。

使用“编辑参数”窗格的 `Previews` 部分查看参数的扩展选项：

![显示时间范围参数选项的图像](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
