---
title: 初学者查询示例
description: 使用 Azure 资源图来运行一些初学者查询，包括计算资源数、对资源进行排序或按特定标记排序。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fd945b5fd9f26cc65c5b049406831228a3d5f327
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338710"
---
# <a name="starter-resource-graph-queries"></a>初学者资源图表查询

了解使用 Azure 资源图表进行查询的第一步是对[查询语言](../concepts/query-language.md)有基本的了解。 如果还不熟悉 [Azure 数据资源管理器](../../../data-explorer/data-explorer-overview.md)，建议查看基础知识，以了解如何撰写所需资源的请求。

我们将逐步介绍以下初学者查询：

> [!div class="checklist"]
> - [对 Azure 资源进行计数](#count-resources)
> - [列出按名称排序的资源](#list-resources)
> - [按降序显示按名称排序的所有虚拟机](#show-vms)
> - [按名称及其 OS 类型显示前五个虚拟机](#show-sorted)
> - [按 OS 类型对虚拟机进行计数](#count-os)
> - [显示包含存储的资源](#show-storage)
> - [列出所有公共 IP 地址](#list-publicip)
> - [对具有由订阅配置的 IP 地址的资源进行计数](#count-resources-by-ip)
> - [列出具有特定标记值的资源](#list-tag)
> - [列出具有特定标记值的所有存储帐户](#list-specific-tag)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>语言支持

Azure CLI（通过扩展）和 Azure PowerShell（通过模块）支持 Azure 资源图表。 在运行以下任何查询之前，请检查环境是否已准备就绪。 有关安装和验证所选 shell 环境的步骤，请参阅 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)。

## <a name="count-resources"></a>对 Azure 资源进行计数

此查询返回有权访问的订阅中存在的 Azure 资源的数量。 这是一个良好查询，用于验证所选 shell 是否已安装适当的 Azure 资源图表组件并处于正常工作状态。

```Query
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="list-resources"></a>列出按名称排序的资源

此查询返回任意类型的资源，但只返回“名称”、“类型”和“位置”属性。 它使用 `order by` 以升序 (`asc`) 按“名称”属性对属性排序。

```Query
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="show-vms"></a>按降序显示按名称排序的所有虚拟机

若要只列出虚拟机（类型为 `Microsoft.Compute/virtualMachines`），我们可在结果中匹配属性“类型”。 与上一查询类似，`desc` 将 `order by` 更改为降序。 类型匹配中的 `=~` 告知资源图表不区分大小写。

```Query
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="show-sorted"></a>按名称及其 OS 类型显示前五个虚拟机

此查询将使用 `limit` 仅检索按名称排序的五条匹配记录。 Azure 资源的类型为 `Microsoft.Compute/virtualMachines`。 `project` 告诉 Azure 资源图表要包含哪些属性。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="count-os"></a>按 OS 类型对虚拟机进行计数

基于前面的查询，我们仍受限于类型 `Microsoft.Compute/virtualMachines` 的 Azure 资源，但不再限制返回的记录数量。
相反，我们使用 `summarize` 和 `count()` 来定义如何按属性对值进行分组和聚合，在此示例中为 `properties.storageProfile.osDisk.osType`。 有关此字符串在完整对象中的外观示例，请参阅[浏览资源 - 虚拟机发现](../concepts/explore-resources.md#virtual-machine-discovery)。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

编写相同查询的另一种方法是 `extend` 属性，并赋予其临时名称，以供查询使用，在本例中为 os。 os 然后由 `summarize` 和 `count()` 使用，如上例所示。

```Query
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> 请注意，虽然 `=~` 允许不区分大小写的匹配，但在查询中使用属性（例如 properties.storageProfile.osDisk.osType）要求大小写正确。 如果属性的大小写不正确，它仍可返回值，但分组或汇总可能不正确。

## <a name="show-storage"></a>显示包含存储的资源

此示例查询不显式定义要匹配的类型，而是查找 `contains` 单词“存储”的任何 Azure 资源。

```Query
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="list-publicip"></a>列出所有公共 IP 地址

与上一查询类似，查找包含单词“publicIPAddresses”的所有类型。
此查询扩展了该模式，以排除 properties.ipAddress 为 NULL 的结果，仅返回 properties.ipAddress，并按前 100 名 `limit` 结果。 根据所选 shell，可能需要转义引号。

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100"
```

## <a name="count-resources-by-ip"></a>对具有由订阅配置的 IP 地址的资源进行计数

使用前面的示例查询并添加 `summarize` 和 `count()`，我们可通过订阅配置了 IP 地址的资源来获取列表。

```Query
where type contains 'publicIPAddresses' and properties.ipAddress != ''
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"
```

## <a name="list-tag"></a>列出具有特定标记值的资源

我们可通过 Azure 资源类型以外的属性（如标记）来限制结果。 在此示例中，我们正在筛选 Azure 资源，其标记名为“环境”，其值为 Internal。

```Query
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

如果还要提供资源具有的标记及其值，请将属性“标记”添加到 `project` 关键字。

```Query
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="list-specific-tag"></a>列出具有特定标记值的所有存储帐户

组合前面示例的筛选功能，按“类型”属性筛选 Azure 资源类型。 此查询还使用特定的标记名称和值来限制对 Azure 资源特定类型的搜索。

```Query
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> 此示例使用 `==` 进行匹配，而不是使用 `=~` 条件。 `==` 是区分大小写的匹配项。

## <a name="next-steps"></a>后续步骤

- 了解有关[查询语言](../concepts/query-language.md)的详细信息
- 了解如何[浏览资源](../concepts/explore-resources.md)
- 查看[高级查询](advanced.md)的示例