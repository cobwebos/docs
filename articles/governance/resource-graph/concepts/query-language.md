---
title: 理解查询语言
description: 介绍可用于 Azure 资源关系图的资源关系图表和可用的 Kusto 数据类型、运算符和函数。
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 80b33212afa7fed3f87b241d5cf69b43be66574d
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755917"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查询语言

Azure Resource Graph 查询语言支持多个运算符和函数。 每个工作和基于[Kusto 查询语言（KQL）](/azure/kusto/query/index)运行。 若要了解资源关系图使用的查询语言，请从[KQL 的教程](/azure/kusto/query/tutorial)开始。

本文介绍资源图支持的语言组件：

- [资源图表表](#resource-graph-tables)
- [支持的 KQL 语言元素](#supported-kql-language-elements)
- [转义字符](#escape-characters)

## <a name="resource-graph-tables"></a>资源图表表

资源图为它存储的数据提供了多个表，这些表用于资源管理器资源类型及其属性。 这些表可以与 `join` 或 `union` 运算符一起使用，以获取相关资源类型的属性。 下面是资源图中可用表的列表：

|资源图表表 |描述 |
|---|---|
|资源 |如果未在查询中定义，则为默认表。 大多数资源管理器资源类型和属性位于此处。 |
|ResourceContainers |包括订阅（预览--`Microsoft.Resources/subscriptions`）和资源组（`Microsoft.Resources/subscriptions/resourcegroups`）资源类型和数据。 |
|AlertsManagementResources |包括与 `Microsoft.AlertsManagement`_相关_的资源。 |
|SecurityResources |包括与 `Microsoft.Security`_相关_的资源。 |

> [!NOTE]
> _资源_是默认表。 查询_资源_表时，不需要提供表名称，除非使用 `join` 或 `union`。 但是，建议的做法是始终在查询中包含初始表。

使用门户中的资源图形资源管理器来发现每个表中有哪些可用的资源类型。 作为替代方法，使用查询（例如 `<tableName> | distinct type`）获取给定资源关系图表支持的资源类型的列表。

下面的查询显示了简单的 `join`。 在此示例中，查询结果将列与联接的表中的所有重复列名称相加， _ResourceContainers_在此示例中追加**1**。 由于_ResourceContainers_表具有订阅和资源组的类型，因此可以使用 type 从_资源_表联接到资源。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

下面的查询显示 `join` 的更复杂用法。 查询将联接表限制为订阅资源并具有 `project`，以仅包括原始字段 _SubscriptionId_ 和重命名为 _SubName_ 的 _name_ 字段。 字段重命名避免了将其添加为_name1_ `join`，因为资源已存在于_资源_中。 原始表使用 `where` 进行筛选，以下 `project` 包括两个表中的列。 查询结果是单个密钥保管库，其中显示密钥保管库的类型、名称以及其所在的订阅的名称。

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> 使用 `project` 限制 `join` 结果时，`join` 用于关联两个表的属性（在上述示例中， _subscriptionId_ ）必须包括在 `project` 中。

## <a name="supported-kql-language-elements"></a>支持的 KQL 语言元素

资源图表支持所有 KQL[数据类型](/azure/kusto/query/scalar-data-types/)、[标量函数](/azure/kusto/query/scalarfunctions)、[标量运算符](/azure/kusto/query/binoperators)和[聚合函数](/azure/kusto/query/any-aggfunction)。 资源图表支持特定的[表格运算符](/azure/kusto/query/queries)，其中一些运算符具有不同的行为。

### <a name="supported-tabulartop-level-operators"></a>支持的表格/顶层运算符

下面是包含特定示例的资源关系图支持的 KQL 表格运算符列表：

|KQL |资源图示例查询 |说明 |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[统计密钥保管库](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[显示特定别名的非重复值](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[按 OS 类型对虚拟机进行计数](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[具有订阅名称的密钥保管库](../samples/advanced.md#join) |支持的联接风格： [innerunique](/azure/kusto/query/joinoperator#default-join-flavor)、 [inner](/azure/kusto/query/joinoperator#inner-join)、 [leftouter](/azure/kusto/query/joinoperator#left-outer-join)。 单个查询中的限制为 3 `join`。 不允许使用自定义联接策略，例如广播联接。 可以在单个表中使用，也可以在 "_资源_" 和 " _ResourceContainers_ " 表之间使用。 |
|[limit](/azure/kusto/query/limitoperator) |[列出所有公共 IP 地址](../samples/starter.md#list-publicip) |@No__t_0 的同义词 |
|[mv 展开](/azure/kusto/query/mvexpandoperator) |[列出具有特定写入位置的 Cosmos DB](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_最大值为400 |
|[为了](/azure/kusto/query/orderoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) |@No__t_0 的同义词 |
|[project](/azure/kusto/query/projectoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[删除结果中的列](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) |@No__t_0 的同义词 |
|[summarize](/azure/kusto/query/summarizeoperator) |[对 Azure 资源进行计数](../samples/starter.md#count-resources) |仅简化的首页 |
|[take](/azure/kusto/query/takeoperator) |[列出所有公共 IP 地址](../samples/starter.md#list-publicip) |@No__t_0 的同义词 |
|[返回页首](/azure/kusto/query/topoperator) |[按名称及其 OS 类型显示前五个虚拟机](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[将两个查询的结果合并为单个结果](../samples/advanced.md#unionresults) |允许使用单个表： _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=`_ColumnName_ 1_表_。 单个查询中的最大值为 3 `union`。 不允许对 `union` 支线表进行模糊解析。 可以在单个表中使用，也可以在 "_资源_" 和 " _ResourceContainers_ " 表之间使用。 |
|[where](/azure/kusto/query/whereoperator) |[显示包含存储的资源](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>转义字符

某些属性名称（如包含 `.` 或 `$` 的属性名称）在查询中必须进行包装或转义，或者属性名称被错误解释，不提供预期结果。

- `.` 包装属性名称，如下所示： `['propertyname.withaperiod']`
  
  包装属性 odata 的示例查询 _。键入_：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-对属性名称中的字符进行转义。 使用的转义字符取决于从运行的 shell 资源关系图。

  - **bash**  -  `\`

    用于转义 bash 中的属性 _\$type_的示例查询：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -不要转义 `$` 字符。

  - **PowerShell** - ``` ` ```

    用于在 PowerShell 中转义属性 _\$type_的示例查询：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用中的语言
- 请参阅[高级查询](../samples/advanced.md)中的高级使用
- 了解如何[浏览资源](explore-resources.md)