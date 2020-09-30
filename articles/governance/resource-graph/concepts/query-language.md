---
title: 理解查询语言
description: 介绍 Resource Graph 表以及可用于 Azure Resource Graph 的 Kusto 数据类型、运算符和函数。
ms.date: 09/30/2020
ms.topic: conceptual
ms.openlocfilehash: ef588bd3fd8afcf1f1139f97d5df2d48a14b4dd9
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578523"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>了解 Azure Resource Graph 查询语言

Azure Resource Graph 查询语言支持多个运算符和函数。 每个运算符和函数的工作原理和操作方式基于 [Kusto 查询语言 (KQL)](/azure/kusto/query/index)。 若要了解 Resource Graph 使用的查询语言，请从 [KQL 教程](/azure/kusto/query/tutorial)开始。

本文介绍 Resource Graph 支持的语言组件：

- [Resource Graph 表](#resource-graph-tables)
- [Resource Graph 自定义语言元素](#resource-graph-custom-language-elements)
- [支持的 KQL 语言元素](#supported-kql-language-elements)
- [查询的作用域](#query-scope)
- [转义字符](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph 表

Resource Graph 为其存储的有关 Azure 资源管理器资源类型及其属性的数据提供多个表。 这些表可以与 `join` 或 `union` 运算符一起使用，以从相关资源类型获取属性。 下面是 Resource Graph 中可用表的列表：

|Resource Graph 表 |说明 |
|---|---|
|资源 |如果未在查询中定义，则为默认表。 此处显示了大多数资源管理器资源类型和属性。 |
|ResourceContainers |包括订阅（预览版 -- `Microsoft.Resources/subscriptions`）和资源组 (`Microsoft.Resources/subscriptions/resourcegroups`) 资源类型和数据。 |
|AdvisorResources |包括与 `Microsoft.Advisor` 相关的资源。 |
|AlertsManagementResources |包括与 `Microsoft.AlertsManagement` 相关的资源。 |
|GuestConfigurationResources |包括与 `Microsoft.GuestConfiguration` 相关的资源。 |
|HealthResources |包括与 `Microsoft.ResourceHealth` 相关的资源。 |
|MaintenanceResources |包括与 `Microsoft.Maintenance` 相关的资源。 |
|SecurityResources |包括与 `Microsoft.Security` 相关的资源。 |

有关包含资源类型的完整列表，请参阅[参考：支持的表和资源类型](../reference/supported-tables-resources.md)。

> [!NOTE]
>  Resources 是默认表。 查询 Resources 表时，无需提供表名称，除非使用 `join` 或 `union`。 但是，建议的做法是始终在查询中包含初始表。

使用门户中的 Resource Graph 资源管理器来发现每个表中有哪些可用的资源类型。 或者，使用查询（如 `<tableName> | distinct type`）来获取环境中存在的给定 Resource Graph 表所支持的资源类型的列表。

以下查询显示了简单的 `join` 用法。 查询结果将列混合在一起，并且联接表中的所有重复列名称（在此示例中为 ResourceContainers）将附加 1。 由于 ResourceContainers 表具有订阅和资源组的类型，因此任何类型都可用于联接到 resources 表中的资源。

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

以下查询显示了 `join` 的更复杂用法。 查询将联接表限制为订阅资源并具有 `project`，以仅包括原始字段 _SubscriptionId_ 和重命名为 _SubName_ 的 _name_ 字段。 字段重命名避免了 `join` 将其添加为 name1，因为该字段已存在于 Resources中。 原始表使用 `where` 进行筛选，以下 `project` 包括两个表中的列。 查询结果是单个密钥保管库，其中显示密钥保管库的类型、名称以及其所在的订阅的名称。

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> 限制具有 `project` 的 `join` 结果时，`join` 用于关联两个表的属性（在上述示例中为 subscriptionId）必须包含在 `project` 中。

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>扩展属性（预览）

作为预览功能，除了 Azure 资源管理器提供的属性以外，Resource Graph 中的某些资源类型还有其他类型相关的属性可供查询。 这组值（称为“扩展属性”）存在于 `properties.extended` 中受支持的资源类型中。 若要查看哪些资源类型具有“扩展属性”，请使用以下查询：

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

示例：通过 `instanceView.powerState.code` 获取虚拟机计数：

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Resource Graph 自定义语言元素

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>共享查询语法（预览）

作为预览功能，[共享查询](../tutorials/create-share-query.md)可在 Resource Graph 查询中直接访问。 在这种情况下，可以创建标准查询作为共享查询并重复使用它们。 若要在 Resource Graph 查询中调用共享查询，请使用 `{{shared-query-uri}}` 语法。 共享查询的 URI 是该查询的“设置”页上的共享查询的资源 ID。 在此示例中，我们的共享查询 URI 是 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS`。
此 URI 指向我们想要在另一个查询中引用的共享查询的订阅、资源组和全名。 此查询与[教程：创建和共享查询](../tutorials/create-share-query.md)中创建的查询相同。

> [!NOTE]
> 无法保存将共享查询作为共享查询引用的查询。

示例 1：仅使用共享查询

此 Resource Graph 查询的结果与存储在共享查询中的查询相同。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

示例 2：将共享查询并入更大的查询中

此查询首先使用共享查询，然后使用 `limit` 进一步限制结果。

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>支持的 KQL 语言元素

Resource Graph 支持部分 KQL [数据类型](/azure/kusto/query/scalar-data-types/)、[标量函数](/azure/kusto/query/scalarfunctions)、[标量运算符](/azure/kusto/query/binoperators)和[聚合函数](/azure/kusto/query/any-aggfunction)。 Resource Graph 支持特定[表格运算符](/azure/kusto/query/queries)，其中一些运算符具有不同的行为。

### <a name="supported-tabulartop-level-operators"></a>支持的表格/顶级运算符

下面是 Resource Graph 支持的 KQL 表格运算符的列表，以及特定示例：

|KQL |Resource Graph 示例查询 |说明 |
|---|---|---|
|[计数](/azure/kusto/query/countoperator) |[对密钥保管库进行计数](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[显示特定别名的非重复值](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[按 OS 类型对虚拟机进行计数](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[具有订阅名称的密钥保管库](../samples/advanced.md#join) |支持的联接类型：[innerunique](/azure/kusto/query/joinoperator#default-join-flavor)、[inner](/azure/kusto/query/joinoperator#inner-join)、[leftouter](/azure/kusto/query/joinoperator#left-outer-join)。 单个查询中的 `join` 限制为 3。 不允许使用自定义联接策略，如广播联接。 可以在单个表中使用，也可以在 Resources 和 ResourceContainers 表中使用。 |
|[limit](/azure/kusto/query/limitoperator) |[列出所有公共 IP 地址](../samples/starter.md#list-publicip) |的同义词 `take` 。 不适用于 [Skip](./work-with-data.md#skipping-records)。 |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | 旧运算符，请改用 `mv-expand`。 RowLimit 最大值为 400。 默认值为 128。 |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[列出具有特定写入位置的 Cosmos DB](../samples/advanced.md#mvexpand-cosmosdb) |RowLimit 最大值为 400。 默认值为 128。 |
|[order](/azure/kusto/query/orderoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) |`sort` 的同义词 |
|[project](/azure/kusto/query/projectoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[删除结果中的列](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[列出按名称排序的资源](../samples/starter.md#list-resources) |`order` 的同义词 |
|[summarize](/azure/kusto/query/summarizeoperator) |[对 Azure 资源进行计数](../samples/starter.md#count-resources) |仅已简化首页 |
|[take](/azure/kusto/query/takeoperator) |[列出所有公共 IP 地址](../samples/starter.md#list-publicip) |的同义词 `limit` 。 不适用于 [Skip](./work-with-data.md#skipping-records)。 |
|[返回页首](/azure/kusto/query/topoperator) |[按名称及其 OS 类型显示前五个虚拟机](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[将两个查询的结果合并为单个结果](../samples/advanced.md#unionresults) |允许使用单个表：_T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`ColumnName\] Table。 单个查询中的 `union` 分支限制为 3。 不允许对 `union` 分支表进行模糊解析。 可以在单个表中使用，也可以在 Resources 和 ResourceContainers 表中使用。 |
|[where](/azure/kusto/query/whereoperator) |[显示包含存储的资源](../samples/starter.md#show-storage) | |

## <a name="query-scope"></a>查询范围

查询返回的资源的订阅范围取决于访问 Resource Graph 的方法。 Azure CLI 和 Azure PowerShell 会根据授权用户的上下文填充要在请求中加入的订阅列表。 可以分别使用 subscriptions 和 Subscription 参数为每个订阅手动定义订阅列表 。
在 REST API 和所有其他 SDK 中，包括资源的订阅列表必须显式定义为请求的一部分。

作为预览版，REST API 版本 `2020-04-01-preview` 会添加一个属性，将查询范围限定到[管理组](../../management-groups/overview.md)。 此预览 API 也使订阅属性成为可选属性。 如果未定义管理组或订阅列表，则查询范围将包括经过身份验证的用户可以访问的所有资源（包括 [Azure Lighthouse](../../../lighthouse/concepts/azure-delegated-resource-management.md) 委派的资源）。 新的 `managementGroupId` 属性采用管理组 ID，该 ID 不同于管理组的名称。 指定 `managementGroupId` 时，将包含在指定管理组层次结构中或其下的前 5000 个订阅的资源。 `managementGroupId` 与 `subscriptions` 不能同时使用。

示例：使用 ID“myMG”查询管理组层次结构中名为“我的管理组”的所有资源。

- REST API URI

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- 请求正文

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>转义字符

某些属性名称（例如，包含 `.` 或 `$` 的名称）必须在查询中进行包装或转义，否则属性名称将被错误解释，并且不会提供预期结果。

- `.` - 包装属性名称，如下所示：`['propertyname.withaperiod']`
  
  用于包装属性 odata.type 的示例查询：

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - 转义属性名称中的字符。 使用的转义字符取决于运行 Resource Graph 的 shell。

  - bash - `\`

    用于在 bash 中转义属性 \$type 的示例查询：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - cmd - 不要转义 `$` 字符。

  - **PowerShell** - ``` ` ```

    用于在 PowerShell 中转义属性 \$type 的示例查询：

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用的语言。
- 请参阅[高级查询](../samples/advanced.md)中的高级用法。
- 详细了解如何[浏览资源](explore-resources.md)。
