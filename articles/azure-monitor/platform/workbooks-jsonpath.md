---
title: Azure Monitor 工作簿 - 使用 JSONPath 转换 JSON 数据
description: 如何使用 Azure Monitor 工作簿中的 JSONPath，将查询的终结点返回的 JSON 数据结果转换为所需格式。
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: efa54933ac7d57ec0dcff9ae11b6fb5c2c87a897
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081383"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>如何在工作簿中使用 JSONPath 转换 JSON 数据

工作簿可以从多个源查询数据。 某些终结点（例如 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)或自定义终结点）可以返回 JSON 格式的结果。 如果查询的终结点返回的 JSON 数据未采用所需的格式进行配置，可以使用 JSONPath 来转换结果。

JSONPath 是适用于 JSON 的一种查询语言，类似于 XML 的 XPath。 与 XPath 类似，JSONPath 允许从 JSON 结构中提取和筛选数据。

工作簿作者可以使用 JSONPath 转换将 JSON 转换为表结构。 然后，可以使用该表来绘制[工作簿可视化效果](workbooks-visualizations.md)。

## <a name="using-jsonpath"></a>使用 JSONPath

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加” > “添加查询”链接将查询控件添加到工作簿。 
3. 选择“JSON”作为数据源。
4. 使用 JSON 编辑器输入以下 JSON 片段
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

假设以商店库存的表示形式提供了上述 JSON 对象。 我们的任务是通过列出书籍的书名、作者和价格，来创建商店供应书籍的表。

1. 选择“结果设置”选项卡，并将结果格式切换为“JSON 路径”。 
2. 应用以下 JSON 路径设置：

    JSON 路径表：`$.store.books`。 此字段表示表的根路径。 在本例中，我们关注的是商店的书籍库存。 表路径将按书籍信息筛选 JSON。

   | 列 ID | 列 JSON 路径 |
   |:-----------|:-----------------|
   | 标题      | `$.title`        |
   | 作者     | `$.author`       |
   | 价格      | `$.price`        |

    列 ID 将是列标题。 “列 JSON 路径”字段表示从表的根到列值的路径。

1. 单击“运行查询”应用上述设置

![ 使用 JSON 数据源和 JSON 路径结果格式编辑查询项](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>后续步骤
- [工作簿概述](workbooks-overview.md)
- [Azure Monitor 工作簿中的组](workbooks-groups.md)
