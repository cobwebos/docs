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
ms.openlocfilehash: 6798ef3f81ff8e733886fccc56f6474afc2ee5e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992484"
---
# <a name="advanced-resource-graph-queries"></a>高级资源图表查询

了解使用 Azure 资源图表进行查询的第一步是对[查询语言](../concepts/query-language.md)有基本的了解。 如果还不熟悉 Kusto 查询语言 (KQL)，建议你查看基础知识，以了解如何撰写所需资源的请求。

我们将逐步介绍以下高级查询：

> [!div class="checklist"]
> - [查找没有磁盘加密或未启用加密的虚拟机规模集](#vmss-not-encrypted)
> - [列出所有标记名称](#list-all-tags)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="language-support"></a>语言支持

Azure CLI（通过扩展）和 Azure PowerShell（通过模块）支持 Azure 资源图表。 在执行以下任何查询之前，请检查环境是否已准备就绪。 有关安装和验证所选 shell 环境的步骤，请参阅 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 和 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)。

## <a name="vmss-not-encrypted"></a>查找没有磁盘加密或未启用加密的虚拟机规模集

此查询将查找虚拟机规模集资源，展开扩展详细信息，查看扩展类型和 EncryptionOperation 属性，汇总并返回预期配置计数为 0 的资源。

```Query
where type =~ 'microsoft.compute/virtualmachinescalesets'
| project id, extension = properties.virtualMachineProfile.extensionProfile.extensions
| where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"'
| project id
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
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

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="next-steps"></a>后续步骤

- 查看[初学者查询](starter.md)示例
- 了解有关[查询语言](../concepts/query-language.md)的详细信息
- 了解如何[浏览资源](../concepts/explore-resources.md)