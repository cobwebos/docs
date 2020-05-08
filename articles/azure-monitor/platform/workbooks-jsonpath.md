---
title: Azure Monitor 工作簿-通过 JSONPath 转换 JSON 数据
description: 如何使用 Azure Monitor 工作簿中的 JSONPath 将查询的终结点返回的 JSON 数据的结果转换为所需的格式。
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 58a2657f6b9aee101384146c4ebb43023953bfcb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892207"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>如何使用 JSONPath 转换工作簿中的 JSON 数据

工作簿可以从多个源查询数据。 某些终结点（如[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)或自定义终结点）可以返回 JSON 形式的结果。 如果查询的终结点返回的 JSON 数据未采用您所需的格式进行配置，则可以使用 JSONPath 来转换结果。

JSONPath 是一种 JSON 查询语言，类似于 XML 的 XPath。 与 XPath 类似，JSONPath 允许从 JSON 结构中提取和筛选数据。

通过使用 JSONPath 转换，工作簿作者可以将 JSON 转换为表结构。 然后，可以使用该表绘制[工作簿可视化](workbooks-visualizations.md)对象。

## <a name="using-jsonpath"></a>使用 JSONPath

1. 单击 "*编辑*" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "*添加* > *添加查询*" 链接向工作簿添加查询控件。
3. 选择 " *JSON*" 作为数据源。
4. 使用 JSON 编辑器输入以下 JSON 代码片段
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

假设我们将上述 JSON 对象作为商店清单的表示形式。 我们的任务是通过列出商店的书名、作者和价格来创建商店可用书籍的表格。

1. 选择 "*结果设置*" 选项卡，并将结果格式切换为 " *JSON 路径*"。
2. 应用以下 JSON 路径设置：

    JSON 路径表： `$.store.books`。 此字段表示表的根的路径。 在这种情况下，我们关心商店的书名。 表路径将 JSON 筛选为书籍信息。

   | 列 Id | 列 JSON 路径 |
   |:-----------|:-----------------|
   | Title      | `$.title`        |
   | 作者     | `$.author`       |
   | 价格      | `$.price`        |

    列 Id 将为列标题。 列 JSON 路径字段表示从表的根到列值的路径。

1. 单击 "*运行查询*" 应用上述设置

![ 编辑具有 JSON 数据源和 JSON 路径结果格式的查询项](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>后续步骤
- [工作簿概述](workbooks-overview.md)
- [Azure Monitor 工作簿中的组](workbooks-groups.md)
