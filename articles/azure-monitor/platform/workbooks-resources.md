---
title: Azure Monitor 工作簿创建交互式报表 |资源参数 |Microsoft 文档
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
ms.openlocfilehash: 2cd170fdc3bed04a81c66d83b9e75ed77e3c0a5a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165844"
---
# <a name="workbook-resource-parameters"></a>工作簿资源参数

资源参数用于在工作簿中选择资源。 在设置从中获取数据的作用域时，这非常有用。 例如，允许用户选择一组 Vm，其中的图表将在呈现数据时使用。

资源选取器中的值可以来自工作簿上下文、静态列表或来自 Azure 资源 Graph 查询。

## <a name="creating-a-resource-parameter-workbook-resources"></a>创建资源参数（工作簿资源）
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `Applications`
    2. 参数类型： `Resource picker`
    3. 必需： `checked`
    4. 允许多种选择： `checked`
5. 从以下来源获取数据： `Workbook Resources`
6. 仅包括资源类型： `Application Insights`
7. 从工具栏中选择 "保存"，创建参数。

![显示使用工作簿资源创建资源参数的图像](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>创建资源参数（Azure 资源图）
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `Applications`
    2. 参数类型： `Resource picker`
    3. 必需： `checked`
    4. 允许多种选择： `checked`
5. 从以下来源获取数据： `Query`
    1. 查询类型： `Azure Resource Graph`
    2. 订阅： `Use default subscriptions`
    3. 在查询控件中添加此代码片段
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 从工具栏中选择 "保存"，创建参数。

![显示如何使用 Azure 资源关系图创建资源参数的图像](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure 资源图尚未在所有云中提供。 如果选择了此方法，请确保在目标云中受支持。

[Azure 资源关系图文档](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>创建资源参数（JSON 列表）
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `Applications`
    2. 参数类型： `Resource picker`
    3. 必需： `checked`
    4. 允许多种选择： `checked`
5. 从以下来源获取数据： `JSON`
    1. 在内容控件中，添加此 json 代码片段
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 单击 "蓝色_更新_" 按钮。
6. （可选）将 `Include only resource types` 设置为_Application Insights_
7. 从工具栏中选择 "保存"，创建参数。

## <a name="referencing-a-resource-parameter"></a>引用资源参数
1. 向工作簿添加查询控件并选择 Application Insights 资源。
2. 使用_Application Insights_下拉控件将参数绑定到控件。 这样做会将查询的作用域设置为运行时参数返回的资源。
4. 在 KQL 控件中，添加此代码片段
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 运行查询以查看结果。 

![显示查询控件中引用的资源参数的图像](./media/workbooks-resources/resource-reference.png)

> 此方法可用于将资源绑定到其他控件（如指标）。

## <a name="resource-parameter-options"></a>资源参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 选定的资源 ID | _/subscriptions/< >/resourceGroups/< 资源组 >/providers/< >/acmeauthentication 的子 id_ |
| `{Applications:label}` | 所选资源的标签 | `acmefrontend` |
| `{Applications:value}` | 所选资源的值 | _"/subscriptions/< 子 id >/resourceGroups/< 资源组 >/providers/</acmeauthentication"_ |
| `{Applications:name}` | 所选资源的名称 | `acmefrontend` |
| `{Applications:resourceGroup}` | 所选资源的资源组 | `acmegroup` |
| `{Applications:resourceType}` | 所选资源的类型 | _microsoft insights/组件_ |
| `{Applications:subscription}` | 所选资源的订阅 |  |
| `{Applications:grid}` | 显示资源属性的网格。 用于在调试时在文本块中呈现  |  |

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。