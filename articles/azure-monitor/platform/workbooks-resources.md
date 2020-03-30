---
title: Azure 监视工作簿资源参数
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658096"
---
# <a name="workbook-resource-parameters"></a>工作簿资源参数

资源参数允许在工作簿中选取资源。 这在设置从中获取数据的范围时非常有用。 一个例子是允许用户选择 VM 集，图表稍后将在呈现数据时使用这些 VM。

来自资源选取器的值可能来自工作簿上下文、静态列表或 Azure 资源图查询。

## <a name="creating-a-resource-parameter-workbook-resources"></a>创建资源参数（工作簿资源）
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`Applications`
    2. 参数类型：`Resource picker`
    3. 必填：`checked`
    4. 允许多种选择：`checked`
5. 从：`Workbook Resources`
6. 仅包括资源类型：`Application Insights`
7. 从工具栏中选择"保存"以创建参数。

![显示使用工作簿资源创建资源参数的图像](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>创建资源参数（Azure 资源图）
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`Applications`
    2. 参数类型：`Resource picker`
    3. 必填：`checked`
    4. 允许多种选择：`checked`
5. 从：`Query`
    1. 查询类型：`Azure Resource Graph`
    2. 订阅：`Use default subscriptions`
    3. 在查询控件中，添加此代码段
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. 从工具栏中选择"保存"以创建参数。

![显示使用 Azure 资源图创建资源参数的图像](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure 资源图尚未在所有云中可用。 如果选择此方法，请确保目标云中支持它。

[Azure 资源图文档](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>创建资源参数（JSON 列表）
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`Applications`
    2. 参数类型：`Resource picker`
    3. 必填：`checked`
    4. 允许多种选择：`checked`
5. 从：`JSON`
    1. 在内容控件中，添加此 json 代码段
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. 点击蓝色_更新_按钮。
6. 可选地将`Include only resource types`设置为_应用程序见解_
7. 从工具栏中选择"保存"以创建参数。

## <a name="referencing-a-resource-parameter"></a>引用资源参数
1. 向工作簿添加查询控件并选择应用程序见解资源。
2. 使用下拉_的应用程序见解_将参数绑定到控件。 执行此操作会将查询的范围设置到运行时参数返回的资源。
4. 在 KQL 控件中，添加此代码段
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
| `{Applications}` | 所选资源 ID | _/订阅/<子 id>/资源组/<资源组>/提供程序/<资源类型>/acme 身份验证_ |
| `{Applications:label}` | 所选资源的标签 | `acmefrontend` |
| `{Applications:value}` | 所选资源的值 | _'/订阅/<子 id>/资源组/<资源组>/提供程序/<资源类型>/acme 身份验证"_ |
| `{Applications:name}` | 所选资源的名称 | `acmefrontend` |
| `{Applications:resourceGroup}` | 所选资源的资源组 | `acmegroup` |
| `{Applications:resourceType}` | 所选资源的类型 | _微软.insights/组件_ |
| `{Applications:subscription}` | 所选资源的订阅 |  |
| `{Applications:grid}` | 显示资源属性的网格。 调试时在文本块中渲染很有用  |  |

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
