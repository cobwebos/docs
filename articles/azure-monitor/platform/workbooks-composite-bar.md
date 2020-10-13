---
title: Azure Monitor 工作簿复合条呈现器
description: 了解所有 Azure Monitor 工作簿复合条呈现器可视化。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 80846ecb1ad48d9f8ba49f0025772b4e131c23e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776351"
---
# <a name="composite-bar-renderer"></a>复合条呈现器

工作簿允许使用复合条显示数据，由多个条形组成的条。

下图显示了数据库状态的复合条，它表示联机、脱机和正在恢复状态下的服务器的数量。

![数据库状态的复合条的屏幕截图。](./media/workbooks-composite-bar/database-status.png)

网格、图块和图形可视化效果支持复合条呈现器。

## <a name="adding-composite-bar-renderer"></a>添加复合条呈现器

1. 通过选择 " *编辑* 工具栏项" 将工作簿切换到编辑模式。
2. 选择 " *添加* "，然后选择 " *添加查询*"。
3. 将 " *数据源* " 设置为 "JSON"，将 *可视化效果* 设置为 "网格"。
4. 添加以下 JSON 数据。

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. 运行查询。
6. 选择 " **列设置** " 打开设置。
7. 从 *列* 中选择 "total"，然后选择 *列呈现*器的 "复合条"。
8. 在 " *复合栏设置*" 下设置以下设置。

| 列名 | 颜色        |
|-------------|--------------|
| 联机      | 绿色        |
| 恢复  | Yellow       |
| offline     | 红色 (鲜)  |

9. 添加标签：`["online"] of ["total"] are healthy`
10. 在 "联机"、"脱机" 和 "恢复" 的 "列设置" 中，可以将列呈现器设置为 "Hidden" (可选) 。
11. 在顶部选择 *标签* ，并将总计列的标签更新为 "数据库状态" (可选) 。
12. 选择 "**应用**"

复合条设置将类似于下面的屏幕截图：

![带有上述设置的复合条形列设置的屏幕截图。](./media/workbooks-composite-bar/composite-bar-settings.png)

带有以上设置的复合条：

![复合条的屏幕截图。](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>复合条设置

选择 "列名称" 和相应的 "颜色"，将该颜色中的列呈现为复合条形图的一部分。 您可以插入、删除和移动行。

### <a name="label"></a>Label

复合栏标签显示在复合栏的顶部。 可以混合使用静态文本、列和参数。  如果标签为空，则当前列的值显示为标签。 在前面的示例中，如果我们将标签字段留空，则会显示 "总列数" 的值。

请参阅的列 `["columnName"]` 。

用引用参数 `{paramName}` 。

列名称和参数名称都区分大小写。 还可以通过选择 "将此项作为链接"，然后添加链接设置来使标签成为链接。

### <a name="aggregation"></a>聚合

聚合对于树/组按可视化效果非常有用。 组行的列数据由该列的聚合集决定。 有三种类型的聚合适用于复合条： None、Sum 和 Inherit。

若要添加组，请执行以下操作：

1. 在 "列设置" 中，找到要向其中添加设置的列。
2. 在树 */组*"树" 下，按 "*树类型*" 下的 "**分组依据**"
3. 选择要按其分组的字段。

![分组依据设置的屏幕截图。](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>无

None 聚合表示没有为组行显示此列的结果。

![没有聚合的复合条的屏幕截图。](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>SUM

如果将聚合设置为 Sum，则组行中的列将使用用于呈现它的列的和显示复合条。 标签还将使用其中所引用的列的总和。

在下面的示例中，联机、脱机和恢复都将最大聚合集设置为，并且合计列的聚合为 sum。

![带有 sum 聚合的复合条的屏幕截图。](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>继承

如果将 "聚合" 设置为 "继承"，则 "组" 行中的列将使用用户对用于呈现它的列的聚合集来显示复合条。 标签中使用的列还使用用户的聚合集。 如果当前列呈现器是复合条并在 (标签中 refereed （如上面示例中的 "total") ），则 sum 将用作该列的聚合。

在下面的示例中，联机、脱机和恢复都设置了 max 聚合，并且 total 列的聚合是继承的。

![带有继承聚合的复合条的屏幕截图。](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>排序

对于网格可视化效果，具有复合条呈现器的列的行排序基于值，该值是用于动态呈现复合栏计算机的列的总和。 在前面的示例中，用于排序的值是联机、恢复和该特定行的脱机列的总和。

## <a name="tiles-visualization"></a>磁贴可视化

1. 选择**Add** "添加*查询*"。
2. 将数据源更改为 JSON 输入 [前一示例](#adding-composite-bar-renderer)中的数据。
3. 将可视化效果更改为 *磁贴*。
4. 运行查询。
5. 选择 " **磁贴设置**"。
6. 在 "磁贴字段" 中选择 " *左* "。
7. 在 " *字段设置*" 下输入以下设置。
    1. 使用列： "server"。
    2. 列呈现器： "Text"。
8. 选择磁贴字段中的 *底部* 。
9. 在 " *字段设置*" 下输入以下设置。
    1. 使用列： "total"。
    2. 列呈现器： "复合条"。
    3. 输入在 "复合栏设置" 下设置以下设置。

    | 列名 | 颜色        |
    |-------------|--------------|
    | 联机      | 绿色        |
    | 恢复  | Yellow       |
    | offline     | 红色 (鲜)  |

    4. 添加标签： `["online"] of ["total"] are healthy` 。
10. 选择“应用”。 

磁贴的复合条设置：

![带有上述设置的复合条磁贴设置的屏幕截图。](./media/workbooks-composite-bar/tiles-settings.png)

带有以上设置的磁贴的复合条形视图如下所示：

![复合条磁贴的屏幕截图。](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>图形可视化

若要为图形可视化生成复合条形呈现器 (类型 Hive 群集) ，请按照下面的说明进行操作。

1. 选择**Add** "添加*查询*"。
2. 将数据源更改为 JSON 输入 [前一示例](#adding-composite-bar-renderer)中的数据。
3. 将可视化效果更改为 *图形*。
4. 运行查询。
5. 选择 " **图形设置**"。
6. 在节点格式设置中选择 *中心内容* 。
7. 在 " *字段设置*" 下输入以下设置。
    1. 使用列： "total"。
    2. 列呈现器： "复合条"。
    3. 在 " *复合栏设置*" 下输入以下设置。

    |列名  |     颜色    |
    |-------------|--------------|
    | 联机      | 绿色        |
    | 恢复  | Yellow       |
    | offline     | 红色 (鲜)  |

   4. 添加标签： `["online"] of ["total"] are healthy` 。
9. 在 " *布局设置*" 下输入以下设置。
    1. 图形类型： **Hive 群集**。
    2. 节点 ID 选择： "服务器"。
    3. 按字段分组： "None"。
    4. 节点大小：100。
    5. 六边形：5之间的边距。
    6. 着色类型类型： **None**。
1. 选择“应用”。 
    
关系图的复合栏设置：

![带有上述设置的复合条形图设置的屏幕截图。](./media/workbooks-composite-bar/graphs-settings.png)

具有以上设置的图形的复合条形视图如下所示：

![包含 hive 群集的复合条形图的屏幕截图。](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>后续步骤

* 使用 Azure 资源管理器[部署](workbooks-automate.md)工作簿。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
