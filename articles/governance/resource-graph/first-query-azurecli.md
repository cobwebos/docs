---
title: 使用 Azure CLI 运行你的第一个 Resource Graph 查询
description: 本文将指导你完成为 Azure CLI 启用 Resource Graph 扩展并运行第一个查询的步骤。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a252dd6698a5e4f724fcbbf821b01f63ab4f529b
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086805"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>使用 Azure CLI 运行你的第一个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是确保为 [Azure CLI](/cli/azure/) 安装了该扩展。 本快速入门将指导你完成将该扩展添加到 Azure CLI 安装的过程。 可以通过安装在本地的 Azure CLI 或通过 [Azure Cloud Shell](https://shell.azure.com) 使用该扩展。

在此过程结束时，你已将该扩展添加到所选的 Azure CLI 安装中，并将运行你的第一个 Resource Graph 查询。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="add-the-resource-graph-extension"></a>添加 Resource Graph 扩展

若要使 Azure CLI 能够查询 Azure Resource Graph，则必须添加该扩展。 此扩展适用于可以使用 Azure CLI 的任何位置，包括 [Windows 10 上的 bash](/windows/wsl/install-win10)、[Cloud Shell](https://shell.azure.com)（独立的或位于门户内的）、[Azure CLI Docker 映像](https://hub.docker.com/r/microsoft/azure-cli/)，也适用于在本地安装了 Azure CLI 的任何位置。

1. 请确保安装最新的 Azure CLI（至少为 **2.0.45**）。 若尚未安装，请遵循[这些说明](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

1. 在所选的 Azure CLI 环境中，使用以下命令导入该扩展：

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 验证该扩展是否已安装以及是否为预期的版本（至少为 **0.1.7**）：

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

将 Azure CLI 扩展添加到所选环境中后，即可尝试一个简单的 Resource Graph 查询。 该查询将返回前五个 Azure 资源，以及每个资源的名称和资源类型。

1. 使用 `graph` 扩展和 `query` 命令运行你的第一个 Azure Resource Graph 查询：

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将查询更新为 `order by` Name 属性：

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 这将首先限制查询结果，然后对其进行排序。

1. 将查询更新为先 `order by` Name 属性，然后再 `limit` 为前五个结果：

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且符合预期的 -- 按 Name 属性排序，但仍限制为前五个结果。

## <a name="cleanup"></a>清理

如果希望从 Azure CLI 环境中删除 Resource Graph 扩展，可使用以下命令：

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> 这不会删除之前下载的扩展文件。 只会从正在运行的 Azure CLI 环境中将其删除。

## <a name="next-steps"></a>后续步骤

- 获取有关[查询语言](./concepts/query-language.md)的详细信息
- 了解如何[浏览资源](./concepts/explore-resources.md)
- 在 [Azure PowerShell](first-query-powershell.md) 中运行你的第一个查询。
- 查看[初学者查询](./samples/starter.md)的示例
- 查看[高级查询](./samples/advanced.md)的示例
- 在 [UserVoice](https://feedback.azure.com/forums/915958-azure-governance) 上提供反馈