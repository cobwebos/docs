---
title: Azure Monitor 工作簿资源参数
description: 了解如何使用资源参数以允许在工作簿中选择资源。 使用资源参数设置从中获取数据的作用域。
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: a4d4e095e065e9f505ba1b9b46f0d31fb1783eb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90972854"
---
# <a name="workbook-resource-parameters"></a>工作簿资源参数

使用资源参数可以选取工作簿中的资源。 在设置从中获取数据的范围时，资源参数非常有用。 例如，用户可以通过资源参数选择 VM 集，然后，图表在呈现数据时可以使用这些 VM。

资源选取器中的值可以来自工作簿上下文、静态列表或 Azure Resource Graph 查询。

## <a name="creating-a-resource-parameter-workbook-resources"></a>创建资源参数（工作簿资源）
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`Applications`
    2. 参数类型：`Resource picker`
    3. 必需：`checked`
    4. 允许多选：`checked`
5. 从以下位置获取数据：`Workbook Resources`
6. 仅包括资源类型：`Application Insights`
7. 从工具栏中选择“保存”以创建参数。

![显示如何使用工作簿资源创建资源参数的插图](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>创建资源参数 (Azure Resource Graph)
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`Applications`
    2. 参数类型：`Resource picker`
    3. 必需：`checked`
    4. 允许多选：`checked`
5. 从以下位置获取数据：`Query`
    1. 查询类型：`Azure Resource Graph`
    2. 订阅：`Use default subscriptions`
    3. 在查询控件中添加此片段
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 从工具栏中选择“保存”以创建参数。

![显示如何使用 Azure Resource Graph 创建资源参数的插图](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph 尚未在所有云中推出。 如果选择此方法，请确保你的目标云支持 Azure Resource Graph。

[Azure Resource Graph 文档](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>创建资源参数（JSON 列表）
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`Applications`
    2. 参数类型：`Resource picker`
    3. 必需：`checked`
    4. 允许多选：`checked`
5. 从以下位置获取数据：`JSON`
    1. 在内容控件中添加此 JSON 片段
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 点击蓝色的“更新”按钮。
6. （可选）将 `Include only resource types` 设置为 _Application Insights_
7. 从工具栏中选择“保存”以创建参数。

## <a name="referencing-a-resource-parameter"></a>引用资源参数
1. 在工作簿中添加查询控件，然后选择 Application Insights 资源。
2. 使用“Application Insights”下拉列表将参数绑定到控件。 这会将查询范围设置为参数在运行时返回的资源。
4. 在 KQL 控件中添加此片段
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. 运行查询以查看结果。 

![显示查询控件中引用的资源参数的插图](./media/workbooks-resources/resource-reference.png)

> 此方法可用于将资源绑定到其他控件（例如指标）。

## <a name="resource-parameter-options"></a>资源参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{Applications}` | 选定的资源 ID | _/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | 所选资源的标签 | `acmefrontend` |
| `{Applications:value}` | 所选资源的值 | _'/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication'_ |
| `{Applications:name}` | 所选资源的名称 | `acmefrontend` |
| `{Applications:resourceGroup}` | 所选资源的资源组 | `acmegroup` |
| `{Applications:resourceType}` | 所选资源的类型 | _microsoft.insights/components_ |
| `{Applications:subscription}` | 所选资源的订阅 |  |
| `{Applications:grid}` | 显示资源属性的网格。 调试时可用于在文本块中呈现内容  |  |

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
