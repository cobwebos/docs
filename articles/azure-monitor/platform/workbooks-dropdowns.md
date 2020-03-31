---
title: Azure 监视器工作簿下拉参数
description: 使用包含下拉参数的预构建和自定义参数化工作簿简化复杂报告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658274"
---
# <a name="workbook-drop-down-parameters"></a>工作簿下拉参数

下拉允许用户从已知集收集一个或多个输入值（例如，选择应用的请求之一）。 下拉提供一种用户友好的方式，用于收集用户提供的任意输入。 下拉列表在交互式报表中启用筛选特别有用。 

指定下拉列表的最简单方法是在参数设置中提供静态列表。 更有趣的方法是通过 KQL 查询动态获取列表。 参数设置还允许您指定它是单选还是多选，如果是多选，则结果集的格式应如何设置（分隔符、报价单等）。

## <a name="creating-a-static-drop-down-parameter"></a>创建静态下拉参数

1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`Environment`
    2. 参数类型：`Drop down`
    3. 必填：`checked`
    4. 允许`multiple selection`：`unchecked`
    5. 从：`JSON`
5. 在 JSON 输入文本块中，插入此 json 代码段：
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 点击蓝色`Update`按钮。
7. 从工具栏中选择"保存"以创建参数。
8. "环境"参数将是三个值的下拉。

    ![显示静态淹没创建的图像](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>使用项目组创建静态下拉列表
如果查询结果/json 包含"组"字段，下拉将显示值组。 请按照上述示例操作，而是使用以下 json：
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>创建动态下拉参数
1. 从编辑模式下的空工作簿开始。
2. 从工作簿中的链接中选择 _"添加参数_"。
3. 单击蓝色 _"添加参数"_ 按钮。
4. 在弹出的新参数窗格中，输入：
    1. 参数名称：`RequestName`
    2. 参数类型：`Drop down`
    3. 必填：`checked`
    4. 允许`multiple selection`：`unchecked`
    5. 从：`Query`
5. 在 JSON 输入文本块中，插入此 json 代码段：

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 点击蓝色`Run Query`按钮。
2. 从工具栏中选择"保存"以创建参数。
3. "请求名称"参数将是应用中所有请求的名称的下拉列表。

    ![显示动态下拉列表创建的图像](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>引用下拉参数
### <a name="in-kql"></a>在 KQL 中
1. 向工作簿添加查询控件并选择应用程序见解资源。
2. 在 KQL 编辑器中，输入此代码段

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 这将在查询评估时间上扩展为：

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 运行查询以查看结果。 或者，将其呈现为图表。

    ![显示 KQL 中引用的下拉列表的图像](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>参数值、标签、选择和组
上面动态下拉参数中使用的查询将返回在下拉列表中忠实地呈现的值列表。 但是，如果您希望选择不同的显示名称或其中一个显示名称，该怎么办？ 下拉参数允许通过值、标签、选择和组列进行此参数。

下面的示例演示如何获取应用程序见解依赖项的列表，其显示名称具有表情符号的样式，选择了第一个，并且按操作名称分组。

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>下拉参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 所选值 | GET 法布里卡姆帐户 |
| `{DependencyName:label}` | 所选标签 | 🌐 GET fabrikam 帐户 |
| `{DependencyName:value}` | 所选值 | GET 法布里卡姆帐户 |

## <a name="multiple-selection"></a>多选
到目前为止，示例显式设置参数，以便在下拉列表中只选择一个值。 下拉参数也支持`multiple selection`- 启用此功能非常简单，`Allow multiple selection`只需检查选项即可。 

用户还可以选择通过`delimiter`和`quote with`设置指定结果集的格式。 默认值仅将值作为此窗体中的集合返回："a"、"b"、"c"。 它们还可以选择限制选择的数量。

引用参数的 KQL 需要更改才能使用结果的格式。 启用它的最常见方法是通过`in`操作员。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

下面是多选下拉工作的示例：

![显示多选下拉参数的图像](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
