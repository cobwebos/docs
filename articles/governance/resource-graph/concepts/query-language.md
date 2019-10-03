---
title: 理解查询语言
description: 介绍可用于 Azure 资源关系图的可用 Kusto 运算符和函数。
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231510"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查询语言

Azure Resource Graph 查询语言支持多个运算符和函数。 每项工作和操作都基于 [Azure 数据资源管理器](../../../data-explorer/data-explorer-overview.md)完成。

了解 Resource Graph 使用的查询语言的最佳方法是，从 Azure 数据资源管理器[查询语言](/azure/kusto/query/index)文档开始入手。 此文档介绍了语言的组织结构以及各种支持的运算符和函数的协同工作原理。

## <a name="supported-tabular-operators"></a>支持的表格运算符

下面是 Resource Graph 中支持的表格运算符列表：

- [计数](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [排序依据](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [返回页首](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>支持的函数

下面是 Resource Graph 中支持的函数列表：

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>转义字符

某些属性名称 (如包含`.`或`$`的属性名称) 在查询中必须进行包装或转义, 或者属性名称解释错误, 不提供预期结果。

- `.`-包装属性名称, 如下所示:`['propertyname.withaperiod']`
  
  包装属性 odata 的示例查询 _。键入_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-对属性名称中的字符进行转义。 使用的转义字符取决于从运行的 shell 资源关系图。

  - **狂欢** - `\`

    在 bash 中对属性 _\$类型_进行转义的示例查询:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -不要转义`$`字符。

  - **PowerShell** - ``` ` ```

    在 PowerShell 中对属性 _\$类型_进行转义的示例查询:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用中的语言
- 请参阅[高级查询](../samples/advanced.md)中的高级使用
- 了解如何[浏览资源](explore-resources.md)