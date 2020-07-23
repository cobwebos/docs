---
title: Azure Monitor 工作簿下拉列表参数
description: 使用包含下拉列表参数的预生成和自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 73b6029dfe52a4b32c9a8ce092fcd284ac1ec0e7
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965023"
---
# <a name="workbook-drop-down-parameters"></a>工作簿下拉列表参数

用户可以使用下拉列表参数从已知的集中收集一个或多个输入值（例如，选择某个应用的请求）。 下拉列表参数提供从用户收集任意输入的用户友好方式。 在交互式报表中启用筛选功能时，下拉列表参数特别有用。 

指定下拉列表参数的最简单方法是在参数设置中提供一个静态列表。 更有意思的方法是通过 KQL 查询动态获取列表。 还可以使用参数设置来指定单选或多选，如果是多选，则还可以指定如何设置结果集的格式（分隔符、引号等）。

## <a name="creating-a-static-drop-down-parameter"></a>创建静态下拉列表参数

1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`Environment`
    2. 参数类型：`Drop down`
    3. 必需：`checked`
    4. 允许 `multiple selection`：`unchecked`
    5. 从以下位置获取数据：`JSON`
5. 在“JSON 输入”文本块中，插入以下 JSON 片段：
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 点击蓝色的 `Update` 按钮。
7. 从工具栏中选择“保存”以创建参数。
8. Environment 参数将是包含三个值的下拉列表。

    ![显示如何创建静态下拉列表参数的插图](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>创建包含项组的静态下拉列表

如果查询结果/JSON 包含“group”字段，则下拉列表将显示值组。 请遵循上面的示例，但改用以下 JSON：

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

![显示分组下拉列表示例的图像](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>创建动态下拉列表参数
1. 从编辑模式下的空工作簿开始操作。
2. 从工作簿内的链接中选择“添加参数”。
3. 单击蓝色的“添加参数”按钮。
4. 在弹出的“新建参数”窗格中，输入：
    1. 参数名称：`RequestName`
    2. 参数类型：`Drop down`
    3. 必需：`checked`
    4. 允许 `multiple selection`：`unchecked`
    5. 从以下位置获取数据：`Query`
5. 在“JSON 输入”文本块中，插入以下 JSON 片段：

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 点击蓝色的 `Run Query` 按钮。
2. 从工具栏中选择“保存”以创建参数。
3. RequestName 参数将是包含应用中所有请求的名称的下拉列表。

    ![显示如何创建动态下拉列表的插图](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>引用下拉列表参数

### <a name="in-kql"></a>在 KQL 中
1. 在工作簿中添加查询控件，然后选择 Application Insights 资源。
2. 在 KQL 编辑器中输入此片段

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 此片段可根据查询评估时间扩展为：

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 运行查询以查看结果。 （可选）以图表形式呈现结果。

    ![显示在 KQL 中引用下拉列表的插图](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>参数值、标签、选项和组
上述动态下拉列表参数中使用的查询只返回下拉列表中真正呈现的值列表。 但如果需要不同的显示名称，或者要选择其中的一个显示名称，该怎么办？ 下拉列表参数可通过值、标签、选项和组列来实现此目的。

以下示例演示如何获取已使用表情符号设置其显示名称样式的 Application Insights 依赖项的列表，选择第一个依赖项，并按操作名称分组依赖项。

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![使用值、标签、选择和组选项显示下拉参数的图像](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>下拉列表参数选项
| 参数 | 说明 | 示例 |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | 选定值 | GET fabrikamaccount |
| `{DependencyName:label}` | 选定标签 | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | 选定值 | GET fabrikamaccount |

## <a name="multiple-selection"></a>多重选择
目前为止，示例将参数显式设置为仅选择下拉列表中的一个值。 下拉列表参数还支持 `multiple selection` - 只需选中 `Allow multiple selection` 选项即可启用此功能。 

用户还可以选择通过 `delimiter` 和 `quote with` 设置指定结果集的格式。 默认设置仅以集合形式返回采用以下格式的值：'a', 'b', 'c'。 用户还可以选择限制选项数目。

需要更改引用参数的 KQL 才能使用结果的格式。 做到这一点的最常用方式是使用 `in` 运算符。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

下面是多选下拉列表的工作示例：

![显示多选下拉列表参数的插图](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
