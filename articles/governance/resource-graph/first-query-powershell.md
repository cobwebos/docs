---
title: 使用 Azure PowerShell 运行第一个查询
description: 本文将指导你完成为 Azure PowerShell 启用 Resource Graph 模块并运行首个查询的步骤。
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a94fe86cd9c2a6e775be1ec4b3d14798e4cac693
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795861"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>使用 Azure PowerShell 运行首个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是查看是否已为 Azure PowerShell 安装了该模块。 本快速入门将指导你完成将该模块添加到 Azure PowerShell 安装的过程。

在此过程结束时，应该已将模块添加到所选的 Azure PowerShell 安装中，并运行首个 Resource Graph 查询。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>添加 Resource Graph 模块

要使 Azure PowerShell 能够查询 Azure Resource Graph，则必须添加该模块。 此模块可以与在本地安装的 PowerShell 以及 [Azure Cloud Shell](https://shell.azure.com) 一起使用，也可以与 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)一起使用。

### <a name="base-requirements"></a>基本要求

Azure Resource Graph 模块需要以下软件：

- Azure PowerShell 1.0.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。

- PowerShellGet 2.0.1 或更高版本。 若尚未安装或更新，请遵循[这些说明](/powershell/gallery/installing-psget)。

### <a name="install-the-module"></a>安装模块

适用于 PowerShell 的 Resource Graph 模块是 **Az.ResourceGraph**。

1. 从管理 PowerShell 提示符运行以下命令：

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. 验证是否已导入该模块且是否为正确版本 (0.7.1)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

将 Azure PowerShell 模块添加到所选环境中后，即可尝试一个简单的 Resource Graph 查询。 该查询将返回前五个 Azure 资源，以及每个资源的名称和资源类型。

1. 使用 `Search-AzGraph` cmdlet 运行首个 Azure Resource Graph 查询：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 将查询更新为 `order by` Name 属性：

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 这将首先限制查询结果，然后对其进行排序。

1. 将查询更新为先 `order by` Name 属性，然后再 `limit` 为前五个结果：

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且符合预期的 -- 按 Name 属性排序，但仍限制为前五个结果。

## <a name="cleanup"></a>清理

若希望从 Azure PowerShell 环境中删除 Resource Graph 模块，可使用以下命令：

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> 该操作不会删除之前下载的模块文件。 只会从正在运行的 PowerShell 会话中将其删除。

## <a name="next-steps"></a>后续步骤

- 获取有关[查询语言](./concepts/query-language.md)的详细信息
- 了解如何[浏览资源](./concepts/explore-resources.md)
- 在 [Azure CLI](first-query-azurecli.md) 中运行首个查询
- 查看[初学者查询](./samples/starter.md)的示例
- 查看[高级查询](./samples/advanced.md)的示例
- 在 [UserVoice](https://feedback.azure.com/forums/915958-azure-governance) 上提供反馈