---
title: 高级 Azure 资源图表查询
description: 使用 Azure 资源图表以运行一些高级查询。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a9281bc333b3edd12501a6813f12b9e7ad1bf59f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219261"
---
# <a name="advanced-resource-graph-queries"></a>高级资源图表查询

了解使用 Azure 资源图表进行查询的第一步是对[查询语言](../concepts/query-language.md)有基本的了解。 如果还不熟悉 [Azure 数据资源管理器](../../../data-explorer/data-explorer-overview.md)，建议查看基础知识，以了解如何撰写所需资源的请求。

我们将逐步介绍以下高级查询：

> [!div class="checklist"]
> - [获取 VMSS 容量和大小](#vmss-capacity)
> - [列出所有标记名称](#list-all-tags)
> - [由正则表达式匹配的虚拟机](#vm-regex)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="language-support"></a>语言支持

Azure CLI（通过扩展）和 Azure PowerShell（通过模块）支持 Azure 资源图表。 在执行以下任何查询之前，请检查环境是否已准备就绪。 有关安装和验证所选 shell 环境的步骤，请参阅 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)。

## <a name="vmss-capacity"></a>获取 VMSS 容量和大小

此查询将查找虚拟机规模集 (VMSS) 资源，并获取各种详细信息，包括规模集的虚拟机大小和容量。 此信息使用 `toint()` 函数将容量强制转换为数字以便可以存储。 这还会重命名返回到自定义的已命名属性的值。

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```powershell
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"> </a>列出所有标记名称

此查询以标记开头，并生成一个 JSON 对象，列出所有唯一标记名称及其对应的类型。

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```powershell
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>由正则表达式匹配的虚拟机

此查询查找与某个[正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)（称为 _regex_）匹配的虚拟机。
**matches regex @** 允许定义要匹配的 regex，即 **^Contoso(.*)[0-9]+$**。 该 regex 定义说明如下：

- `^` - 匹配项必须以该字符串的开头开头。
- `Contoso` - 要匹配的核心字符串（区分大小写）。
- `(.*)` - 子表达式匹配项：
  - `.` - 匹配任何单一字符（换行符除外）。
  - `*` - 匹配上一个元素零次或多次。
- `[0-9]` - 数字 0 到 9 的字符组匹配项。
- `+` - 匹配上一个元素一次或多次。
- `$` - 上一个元素的匹配项必须出现在该字符串的末尾。

按名称进行匹配后，查询将对名称进行投影并按名称升序排序。

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>后续步骤

- 查看[初学者查询](starter.md)的示例
- 了解有关[查询语言](../concepts/query-language.md)的详细信息
- 了解如何[浏览资源](../concepts/explore-resources.md)