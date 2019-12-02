---
title: 快速入门：第一个 Azure CLI 查询
description: 本快速入门介绍为 Azure CLI 启用 Resource Graph 扩展并运行第一个查询的步骤。
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: e75152c720d94f084b43f855452e5e8ce4dc6bc8
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304120"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>快速入门：使用 Azure CLI 运行你的第一个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是确保为 [Azure CLI](/cli/azure/) 安装了该扩展。 本快速入门将指导你完成将该扩展添加到 Azure CLI 安装的过程。 可以通过安装在本地的 Azure CLI 或通过 [Azure Cloud Shell](https://shell.azure.com) 使用该扩展。

在此过程结束时，你已将该扩展添加到所选的 Azure CLI 安装中，并将运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>添加 Resource Graph 扩展

若要使 Azure CLI 能够查询 Azure Resource Graph，则必须添加该扩展。 此扩展适用于可以使用 Azure CLI 的任何位置，包括 [Windows 10 上的 bash](/windows/wsl/install-win10)、[Cloud Shell](https://shell.azure.com)（独立的或位于门户内的）、[Azure CLI Docker 映像](https://hub.docker.com/r/microsoft/azure-cli/)，也适用于在本地安装了 Azure CLI 的任何位置。

1. 请确保安装最新的 Azure CLI（至少为 2.0.76）  。 若尚未安装，请遵循[这些说明](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

1. 在所选的 Azure CLI 环境中，使用以下命令导入该扩展：

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 验证该扩展是否已安装以及是否为预期的版本（至少为 **1.0.0**）：

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

将 Azure CLI 扩展添加到所选环境中后，即可尝试一个简单的 Resource Graph 查询。 该查询将返回前五个 Azure 资源，以及每个资源的名称和资源类型   。

1. 使用 `graph` 扩展和 `query` 命令运行你的第一个 Azure Resource Graph 查询：

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将查询更新为 `order by` Name 属性  ：

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 这将首先限制查询结果，然后对其进行排序。

1. 将查询更新为先 `order by` Name 属性，然后再 `limit` 为前五个结果  ：

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且符合预期的 -- 按 Name 属性排序，但仍限制为前五个结果  。

## <a name="clean-up-resources"></a>清理资源

如果希望从 Azure CLI 环境中删除 Resource Graph 扩展，可使用以下命令：

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将 Resource Graph 扩展添加到 Azure CLI 环境并运行第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)