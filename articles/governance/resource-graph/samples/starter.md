---
title: 初学者查询示例
description: 使用 Azure 资源图来运行一些初学者查询，包括计算资源数、对资源进行排序或按特定标记排序。
ms.date: 04/27/2020
ms.topic: sample
ms.openlocfilehash: fc499f466d61fb665cc31075a2c310372d993f2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82185857"
---
# <a name="starter-resource-graph-query-samples"></a>初学者 Resource Graph 查询示例

了解使用 Azure 资源图表进行查询的第一步是对[查询语言](../concepts/query-language.md)有基本的了解。 如果还不熟悉 [Kusto 查询语言 (KQL)](/azure/kusto/query/index)，建议查看 [KQL 的教程](/azure/kusto/query/tutorial)，了解如何撰写所需资源的请求。

我们将逐步介绍以下初学者查询：

> [!div class="checklist"]
> - [对 Azure 资源进行计数](#count-resources)
> - [统计密钥保管库资源](#count-keyvaults)
> - [列出按名称排序的资源](#list-resources)
> - [按降序显示按名称排序的所有虚拟机](#show-vms)
> - [按名称及其 OS 类型显示前五个虚拟机](#show-sorted)
> - [按 OS 类型对虚拟机进行计数](#count-os)
> - [显示包含存储的资源](#show-storage)
> - [列出所有公共 IP 地址](#list-publicip)
> - [对具有由订阅配置的 IP 地址的资源进行计数](#count-resources-by-ip)
> - [列出具有特定标记值的资源](#list-tag)
> - [列出具有特定标记值的所有存储帐户](#list-specific-tag)
> - [显示虚拟机资源的别名](#show-aliases)
> - [显示特定别名的非重复值](#distinct-alias-values)
> - [显示未关联的网络安全组](#unassociated-nsgs)
> - [从 Azure 顾问获取成本节约摘要](#advisor-savings)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="language-support"></a>语言支持

Azure CLI（通过扩展）和 Azure PowerShell（通过模块）支持 Azure 资源图表。 在运行以下任何查询之前，请检查环境是否已准备就绪。 有关安装和验证所选 shell 环境的步骤，请参阅 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)。

## <a name="count-azure-resources"></a><a name="count-resources" /> 对 Azure 资源进行计数

此查询返回有权访问的订阅中存在的 Azure 资源的数量。 这是一个良好查询，用于验证所选 shell 是否已安装适当的 Azure 资源图表组件并处于正常工作状态。

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults" />统计密钥保管库资源

此查询使用 `count` 而不是 `summarize` 来计算返回的记录数。 只有密钥保管库才包括在计数中。

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources" /> 列出按名称排序的资源

此查询返回任意类型的资源，但只返回“名称”、“类型”和“位置”属性。    它使用 `order by` 以升序 (`asc`) 按“名称”属性对属性排序。

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" /> 按降序显示按名称排序的所有虚拟机

若要只列出虚拟机（类型为 `Microsoft.Compute/virtualMachines`），我们可在结果中匹配属性“类型”  。 与上一查询类似，`desc` 将 `order by` 更改为降序。 类型匹配中的 `=~` 告知资源图表不区分大小写。

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" /> 按名称及其 OS 类型显示前五个虚拟机

此查询将使用 `top` 仅检索按名称排序的五条匹配记录。 Azure 资源的类型为 `Microsoft.Compute/virtualMachines`。 `project` 告诉 Azure 资源图表要包含哪些属性。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os" /> 按 OS 类型对虚拟机进行计数

基于前面的查询，我们仍受限于类型 `Microsoft.Compute/virtualMachines` 的 Azure 资源，但不再限制返回的记录数量。
相反，我们使用 `summarize` 和 `count()` 来定义如何按属性对值进行分组和聚合，在此示例中为 `properties.storageProfile.osDisk.osType`。 有关此字符串在完整对象中的外观示例，请参阅[浏览资源 - 虚拟机发现](../concepts/explore-resources.md#virtual-machine-discovery)。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

编写相同查询的另一种方法是 `extend` 属性，并赋予其临时名称，以供查询使用，在本例中为 os  。 os 然后由 `summarize` 和 `count()` 使用，如上例所示  。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

> [!NOTE]
> 请注意，虽然 `=~` 允许不区分大小写的匹配，但在查询中使用属性（例如 properties.storageProfile.osDisk.osType）要求大小写正确  。 如果属性的大小写不正确，则会返回 null 值或不正确值，但分组或汇总可能不正确。

## <a name="show-resources-that-contain-storage"></a><a name="show-storage" /> 显示包含存储的资源

此示例查询不显式定义要匹配的类型，而是查找 `contains` 单词“存储”的任何 Azure 资源  。

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip" /> 列出所有公共 IP 地址

与上一查询类似，查找包含单词“publicIPAddresses”的所有类型  。
此查询扩展了该模式，以仅包括 properties.ipAddress 为
`isnotempty` 的结果，仅返回 properties.ipAddress，并将结果`limit`为前 100 名  
100. 根据所选 shell，可能需要转义引号。

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" /> 对具有由订阅配置的 IP 地址的资源进行计数

使用前面的示例查询并添加 `summarize` 和 `count()`，我们可通过订阅配置了 IP 地址的资源来获取列表。

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag" /> 列出具有特定标记值的资源

我们可通过 Azure 资源类型以外的属性（如标记）来限制结果。 在此示例中，我们正在筛选 Azure 资源，其标记名为“环境”，其值为 Internal   。

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

如果还要提供资源具有的标记及其值，请将属性“标记”添加到 `project` 关键字  。

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" /> 列出具有特定标记值的所有存储帐户

组合前面示例的筛选功能，按“类型”属性筛选 Azure 资源类型  。 此查询还使用特定的标记名称和值来限制对 Azure 资源特定类型的搜索。

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

> [!NOTE]
> 此示例使用 `==` 进行匹配，而不是使用 `=~` 条件。 `==` 是区分大小写的匹配项。

## <a name="show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" /> 显示虚拟机资源的别名

Azure Policy 使用 [Azure Policy 别名](../../policy/concepts/definition-structure.md#aliases)管理资源符合性。 Azure Resource Graph 可以返回资源类型的别名  。 创建自定义策略定义后，这些值可用于比较别名的当前值。 默认情况下，查询结果中不提供别名数组  。 使用 `project aliases` 将其显式添加到结果中。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" /> 显示特定别名的非重复值

在单个资源上查看别名的值，这非常有用，但这不会显示使用 Azure Resource Graph 在订阅中进行查询的真实值。 本示例查看特定别名的所有值，并返回不同的值。

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />显示未关联的网络安全组

此查询返回未与网络接口或子网关联的网络安全组 (NSG)。

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings" />从 Azure 顾问获取成本节约情况摘要

此查询汇总了每个 [Azure 顾问](../../../advisor/advisor-overview.md)建议的成本节约情况。

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[门户](#tab/azure-portal)

![Resource Graph 浏览器图标](../media/resource-graph-small.png) 在 Azure Resource Graph 浏览器中尝试此查询：

- Azure 门户：<a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%20%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%20%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%20%7C%20extend%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%20%7C%20summarize%20dcount%28resources%29%2C%20bin%28sum%28savings%29%2C%200.01%29%20by%20solution%2C%20currency%20%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%20%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 政府门户：<a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%20%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%20%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%20%7C%20extend%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%20%7C%20summarize%20dcount%28resources%29%2C%20bin%28sum%28savings%29%2C%200.01%29%20by%20solution%2C%20currency%20%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%20%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.us</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)
- Azure 中国门户：<a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%20%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%20%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%20%7C%20extend%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%20%7C%20summarize%20dcount%28resources%29%2C%20bin%28sum%28savings%29%2C%200.01%29%20by%20solution%2C%20currency%20%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%20%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.cn</a> ![“在新窗口中打开链接”图标](../../media/new-window.png)

---

## <a name="next-steps"></a>后续步骤

- 详细了解[查询语言](../concepts/query-language.md)。
- 详细了解如何[浏览资源](../concepts/explore-resources.md)。
- 查看[高级查询](advanced.md)的示例。