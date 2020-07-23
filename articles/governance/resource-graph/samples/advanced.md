---
title: 高级查询示例
description: 使用 Azure Resource Graph 运行一些高级查询，包括使用列、列出使用的标记以及使用正则表达式匹配资源。
ms.date: 07/06/2020
ms.topic: sample
ms.openlocfilehash: 5784209082c628a17aa2e0118a17b8f00e4bc169
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85961248"
---
# <a name="advanced-resource-graph-query-samples"></a>Advanced Resource Graph 查询示例

了解使用 Azure 资源图表进行查询的第一步是对[查询语言](../concepts/query-language.md)有基本的了解。 如果还不熟悉 [Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview)，建议查看基础知识，以了解如何撰写所需资源的请求。

我们将逐步介绍以下高级查询：

- [显示每个资源类型的 API 版本](#apiversion)
- [获取虚拟机规模集容量和大小](#vmss-capacity)
- [删除结果中的列](#remove-column)
- [列出所有标记名称](#list-all-tags)
- [由正则表达式匹配的虚拟机](#vm-regex)
- [列出具有特定写入位置的 Cosmos DB](#mvexpand-cosmosdb)
- [具有订阅名称的密钥保管库](#join)
- [列出 SQL 数据库及其弹性池](#join-sql)
- [列出其网络接口和公共 IP 的虚拟机](#join-vmpip)
- [列出虚拟机上安装的所有扩展](#join-vmextension)
- [在资源组中查找具有特定标记的存储帐户](#join-findstoragetag)
- [将两个查询的结果合并为单个结果](#unionresults)
- [使用 DisplayNames 包括租户和订阅名称](#displaynames)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="language-support"></a>语言支持

Azure CLI（通过扩展）和 Azure PowerShell（通过模块）支持 Azure 资源图表。 在运行以下任何查询之前，请检查环境是否已准备就绪。 有关安装和验证所选 shell 环境的步骤，请参阅 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)。

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion"></a>显示资源类型和 API 版本

在更新过程中，Resource Graph 主要使用资源提供程序的最新非预览版 API 来 `GET` 资源属性。 在某些情况下，会覆盖所使用的 API 版本，以便在结果中提供更多当前或广泛使用的属性。 以下查询详述了用于在每个资源类型上收集属性的 API 版本：

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20distinct%20type%2C%20apiVersion%0D%0A%7C%20where%20isnotnull%28apiVersion%29%0D%0A%7C%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity"></a>获取虚拟机规模集容量和大小

此查询将查找虚拟机规模集资源，并获取各种详细信息，包括规模集的虚拟机大小和容量。 此查询使用 `toint()` 函数将容量强制转换为数字以供排序。 最后会将列重命名为自定义命名属性。

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%3D~%20%27microsoft.compute%2Fvirtualmachinescalesets%27%0D%0A%7C%20where%20name%20contains%20%27contoso%27%0D%0A%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint%28sku.capacity%29%2C%20Tier%20%3D%20sku.name%0D%0A%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="remove-columns-from-results"></a><a name="remove-column"></a>删除结果中的列

以下查询使用 `summarize` 按订阅对资源进行计数，使用 `join` 将其与 _ResourceContainers_ 表中的订阅详细信息合并，然后使用 `project-away` 删除某些列。

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20resourceCount%3Dcount%28%29%20by%20subscriptionId%0D%0A%7C%20join%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-all-tag-names"></a><a name="list-all-tags"></a>列出所有标记名称

此查询以标记开头，并生成一个 JSON 对象，列出所有唯一标记名称及其对应的类型。

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20tags%0D%0A%7C%20summarize%20buildschema%28tags%29" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex"></a>由正则表达式匹配的虚拟机

此查询查找与某个[正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)（称为 _regex_）匹配的虚拟机。 可以使用 **matches regex \@** 定义要匹配的正则表达式，即 `^Contoso(.*)[0-9]+$`。
该 regex 定义说明如下：

- `^` - 匹配项必须以该字符串的开头开头。
- `Contoso` - 区分大小写的字符串。
- `(.*)` - 子表达式匹配项：
  - `.` - 匹配任何单一字符（换行符除外）。
  - `*` - 匹配上一个元素零次或多次。
- `[0-9]` - 数字 0 到 9 的字符组匹配项。
- `+` - 匹配上一个元素一次或多次。
- `$` - 上一个元素的匹配项必须出现在该字符串的末尾。

按名称进行匹配后，查询将对名称进行投影并按名称升序排序。

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%20and%20name%20matches%20regex%20%40%27%5EContoso%28.%2A%29%5B0-9%5D%2B%24%27%0D%0A%7C%20project%20name%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb"></a>列出具有特定写入位置的 Cosmos DB

以下查询限制了 Cosmos DB 资源，使用 `mv-expand` 展开 **properties.writeLocations** 的属性包，然后投影特定字段，并将结果进一步限制为与 ‘East US’ 或 ‘West US’ 匹配的 **properties.writeLocations.locationName** 值。

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.documentdb%2Fdatabaseaccounts%27%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20%28properties.writeLocations%29%0D%0A%7C%20mv-expand%20writeLocations%0D%0A%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring%28writeLocations.locationName%29%0D%0A%7C%20where%20writeLocation%20in%20%28%27East%20US%27%2C%20%27West%20US%27%29%0D%0A%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="key-vaults-with-subscription-name"></a><a name="join"></a>具有订阅名称的密钥保管库

以下查询演示了 `join`（“类型”为“leftouter”）的复杂用法。 查询将联接表限制为订阅资源并具有 `project`，以仅包括原始字段 _SubscriptionId_ 和重命名为 _SubName_ 的 _name_ 字段。 字段重命名避免了 `join` 将其添加为 _name1_，因为该字段已存在于_资源_中。 原始表使用 `where` 进行筛选，以下 `project` 包括两个表中的列。 查询结果是所有密钥保管库，其中显示密钥保管库的类型、名称以及其所在订阅的名称。

```kusto
Resources
| join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join kind=leftouter (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20join%20kind%3Dleftouter%20%28ResourceContainers%20%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%27%20%7C%20project%20SubName%3Dname%2C%20subscriptionId%29%20on%20subscriptionId%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20project%20type%2C%20name%2C%20SubName" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql"></a>列出 SQL 数据库及其弹性池

以下查询使用 leftouter `join` 将 SQL 数据库资源及其相关弹性池组合在一起（如果有）。

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Fdatabases%27%0D%0A%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower%28tostring%28properties.elasticPoolId%29%29%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.sql%2Fservers%2Felasticpools%27%0D%0A%20%20%20%20%7C%20project%20elasticPoolId%20%3D%20tolower%28id%29%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state%29%0D%0Aon%20elasticPoolId%0D%0A%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip">列出具有其网络接口和公共 IP 的虚拟机</a>

此查询使用两个 **leftouter** `join` 命令将虚拟机、其相关网络接口以及与这些网络接口相关的任何公共 IP 地址组合在一起。

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mv-expand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mv-expand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.compute%2Fvirtualmachines%27%0D%0A%7C%20extend%20nics%3Darray_length%28properties.networkProfile.networkInterfaces%29%20%0D%0A%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%0D%0A%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20%27true%27%20or%20isempty%28nic%29%20%0D%0A%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring%28properties.hardwareProfile.vmSize%29%2C%20nicId%20%3D%20tostring%28nic.id%29%20%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fnetworkinterfaces%27%0D%0A%20%20%20%20%7C%20extend%20ipConfigsCount%3Darray_length%28properties.ipConfigurations%29%20%0D%0A%20%20%20%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%0D%0A%20%20%20%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20%27true%27%0D%0A%20%20%20%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring%28ipconfig.properties.publicIPAddress.id%29%29%0D%0Aon%20nicId%0D%0A%7C%20project-away%20nicId1%0D%0A%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%0D%0A%7C%20join%20kind%3Dleftouter%20%28%0D%0A%20%20%20%20Resources%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.network%2Fpublicipaddresses%27%0D%0A%20%20%20%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress%29%0D%0Aon%20publicIpId%0D%0A%7C%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="list-all-extensions-installed-on-a-virtual-machine"></a><a name="join-vmextension"></a>列出虚拟机上安装的所有扩展

首先，此查询针对虚拟机资源类型使用 `extend` 以获取大写的 (`toupper()`) ID、获取操作系统名称和类型，以及获取虚拟机大小。
获取大写的资源 ID 是准备联接另一个属性的好方法。 然后，此查询将 `join` 用于为 leftouter 的 kind，通过匹配扩展 ID 的大写 `substring` 来获取虚拟机扩展。 “/extensions/\<ExtensionName\>”之前的 ID 部分与虚拟机 ID 的格式相同，因此我们将此属性用于 `join`。 然后会针对虚拟机扩展的名称将 `summarize` 用于 `make_list` 以合并每个扩展的名称，其中 id、OSName、OSType和 VMSize 在单个数组属性中是相同的   。 最后，我们通过 asc 对小写的 OSName 进行 `order by` 操作。 默认情况下，`order by` 为降序。

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| extend
    JoinID = toupper(id),
    OSName = tostring(properties.osProfile.computerName),
    OSType = tostring(properties.storageProfile.osDisk.osType),
    VMSize = tostring(properties.hardwareProfile.vmSize)
| join kind=leftouter(
    Resources
    | where type == 'microsoft.compute/virtualmachines/extensions'
    | extend 
        VMId = toupper(substring(id, 0, indexof(id, '/extensions'))),
        ExtensionName = name
) on $left.JoinID == $right.VMId
| summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize
| order by tolower(OSName) asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend VMId = toupper(substring(id, 0, indexof(id, '/extensions'))), ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type == 'microsoft.compute/virtualmachines' | extend JoinID = toupper(id), OSName = tostring(properties.osProfile.computerName), OSType = tostring(properties.storageProfile.osDisk.osType), VMSize = tostring(properties.hardwareProfile.vmSize) | join kind=leftouter( Resources | where type == 'microsoft.compute/virtualmachines/extensions' | extend VMId = toupper(substring(id, 0, indexof(id, '/extensions'))), ExtensionName = name ) on $left.JoinID == $right.VMId | summarize Extensions = make_list(ExtensionName) by id, OSName, OSType, VMSize | order by tolower(OSName) asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0A%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines'%0A%7C%20extend%0A%20%20%20%20JoinID%20%3D%20toupper(id)%2C%0A%20%20%20%20OSName%20%3D%20tostring(properties.osProfile.computerName)%2C%0A%20%20%20%20OSType%20%3D%20tostring(properties.storageProfile.osDisk.osType)%2C%0A%20%20%20%20VMSize%20%3D%20tostring(properties.hardwareProfile.vmSize)%0A%7C%20join%20kind%3Dleftouter(%0A%20%20%20%20Resources%0A%20%20%20%20%7C%20where%20type%20%3D%3D%20'microsoft.compute%2Fvirtualmachines%2Fextensions'%0A%20%20%20%20%7C%20extend%20%0A%20%20%20%20%20%20%20%20VMId%20%3D%20toupper(substring(id%2C%200%2C%20indexof(id%2C%20'%2Fextensions')))%2C%0A%20%20%20%20%20%20%20%20ExtensionName%20%3D%20name%0A)%20on%20%24left.JoinID%20%3D%3D%20%24right.VMId%0A%7C%20summarize%20Extensions%20%3D%20make_list(ExtensionName)%20by%20id%2C%20OSName%2C%20OSType%2C%20VMSize%0A%7C%20order%20by%20tolower(OSName)%20asc" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag"></a>在资源组上查找具有特定标记的存储帐户

以下查询使用 inner `join` 将存储帐户连接到具有指定标记名称和标记值（区分大小写）的资源组。

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20where%20tags%5B%27Key1%27%5D%20%3D~%20%27Value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

如果必须查找不区分大小写的标记名称和标记值，请将 `mv-expand` 与 **bagexpansion** 参数配合使用。 此查询使用的配额高于前一查询，因此只在必要的时候使用 `mv-expand`。

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%0D%0A%7C%20join%20kind%3Dinner%20%28%0D%0A%20%20%20%20ResourceContainers%0D%0A%20%20%20%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%0D%0A%20%20%20%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%0D%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0D%0A%20%20%20%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%0D%0A%20%20%20%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%0D%0Aon%20subscriptionId%2C%20resourceGroup%0D%0A%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults"></a>将两个查询的结果合并为单个结果

以下查询使用 `union` 从 _ResourceContainers_ 表中获取结果，并将它们添加到 _Resources_ 表中的结果。

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: 在 Azure Resource Graph 资源管理器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">portal.azure.com</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">portal.azure.us</a> :::image type="icon" source="../../media/new-window.png":::
- Azure 中国世纪互联门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%0D%0A%7C%20where%20type%3D%3D%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%0D%0A%7C%20union%20%20%28Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205%29" target="_blank">portal.azure.cn</a> :::image type="icon" source="../../media/new-window.png":::

---

## <a name="include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames"></a>使用 DisplayNames 包括租户和订阅名称

此查询使用新的 **Include** 参数和选项 _DisplayNames_ 将 **subscriptionDisplayName** 和 **tenantDisplayName** 添加到结果中。 此参数仅可用于 Azure CLI 和 Azure PowerShell。

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

获取订阅名称的替代方法是使用 `join` 运算符并连接到 **ResourceContainers** 表和 `Microsoft.Resources/subscriptions` 类型。 `join` 在 Azure CLI、Azure PowerShell、门户和所有受支持的 SDK 中都可以工作。 有关示例，请参阅[示例 - 带订阅名称的密钥保管库](#join)。

> [!NOTE]
> 如果查询未使用 **project** 指定返回的属性，则 **subscriptionDisplayName** 和 **tenantDisplayName** 将自动包括在结果中。
> 如果查询确实使用了 **project**，则每个 _DisplayName_ 字段必须显式包含在 **project** 中，否则它们将不会在结果中返回，即使使用了 **Include** 参数也是如此。 **Include** 参数对[表](../concepts/query-language.md#resource-graph-tables)不起作用。

## <a name="next-steps"></a>后续步骤

- 查看[初级查询](starter.md)的示例。
- 详细了解[查询语言](../concepts/query-language.md)。
- 详细了解如何[浏览资源](../concepts/explore-resources.md)。