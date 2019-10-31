---
title: 了解 Azure Monitor 工作簿下拉参数的详细信息 |Microsoft 文档
description: 通过包含 dropdown 参数的预生成的自定义参数化工作簿简化复杂报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20a1c5f4b4ef12f81e801769a2ee1b5f08860e38
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165207"
---
# <a name="workbook-drop-down-parameters"></a>工作簿下拉参数

下拉请求允许用户从已知集收集一个或多个输入值（例如，选择一个应用的请求）。 下拉一向用户提供了一种从用户那里收集任意输入的方法。 在交互式报表中启用筛选功能时，拖放功能特别有用。 

指定下拉列表的最简单方法是在参数设置中提供静态列表。 更有趣的方法是通过 KQL 查询动态地获取列表。 参数设置还允许您指定它是单选还是多选，如果是多重选择，则为如何设置结果集的格式（分隔符、引号等）。

## <a name="creating-a-static-drop-down-parameter"></a>创建静态下拉参数

1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `Environment`
    2. 参数类型： `Drop down`
    3. 必需： `checked`
    4. 允许 `multiple selection`： `unchecked`
    5. 从以下来源获取数据： `JSON`
5. 在 JSON 输入文本块中，插入以下 json 代码片段：
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. 按蓝色 `Update` 按钮。
7. 从工具栏中选择 "保存"，创建参数。
8. 环境参数将是具有三个值的下拉箭头。

    ![显示静态 drown 的创建的图像](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>使用项组创建静态下拉列表
如果查询结果/json 包含 "组" 字段，则下拉列表将显示值组。 请按照上面的示例进行操作，但改用以下 json：
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
1. 在编辑模式下从空工作簿开始。
2. 从工作簿内的链接中选择 "_添加参数_"。
3. 单击蓝色的 "_添加参数_" 按钮。
4. 在弹出的 "新建参数" 窗格中，输入：
    1. 参数名称： `RequestName`
    2. 参数类型： `Drop down`
    3. 必需： `checked`
    4. 允许 `multiple selection`： `unchecked`
    5. 从以下来源获取数据： `Query`
5. 在 JSON 输入文本块中，插入以下 json 代码片段：

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. 按蓝色 `Run Query` 按钮。
2. 从工具栏中选择 "保存"，创建参数。
3. RequestName 参数将是应用中所有请求的名称的下拉名称。

    ![显示动态下拉菜单创建的图像](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>引用下拉参数
### <a name="in-kql"></a>在 KQL 中
1. 向工作簿添加查询控件并选择 Application Insights 资源。
2. 在 KQL 编辑器中输入此代码片段

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. 这会将查询计算时间扩展到：

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. 运行查询以查看结果。 根据需要，将其呈现为图表。

    ![显示 KQL 中引用的下拉项的图像](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>参数值、标签、选择和组
以上动态下拉参数中所使用的查询只返回下拉列表中切实呈现的值的列表。 但如果需要不同的显示名称，或者要选择其中的一个显示名称，该怎么办？ 下拉参数可通过值、标签、选择列和组列来实现此项。

下面的示例演示如何获取 Application Insights 依赖项的列表，这些依赖项的显示名称具有表情符号，并选择第一个，并按操作名称进行分组。

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
| `{DependencyName}` | 选定值 | 获取 fabrikamaccount |
| `{DependencyName:label}` | 所选标签 | 🌐获取 fabrikamaccount |
| `{DependencyName:value}` | 选定值 | 获取 fabrikamaccount |

## <a name="multiple-selection"></a>多重选择
到目前为止，这些示例将参数显式设置为仅在下拉中选择一个值。 下拉参数还支持 `multiple selection`-启用此功能与检查 `Allow multiple selection` 选项一样简单。 

用户还可以选择通过 "`delimiter`" 和 "`quote with`" 设置来指定结果集的格式。 默认情况下，在此窗体中以集合的形式返回值： "a"、"b"、"c"。 它们还可以选择限制选择的数目。

引用参数的 KQL 需要更改为使用结果格式。 启用该方法的最常见方式是通过 `in` 运算符。

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

下面是一个用于在工作中进行多选下拉的示例：

![显示多选下拉参数的图像](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
