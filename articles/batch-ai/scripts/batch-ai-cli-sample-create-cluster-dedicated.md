---
title: Azure CLI 脚本示例 - 创建专用 Batch AI 群集 | Microsoft Docs
description: Azure CLI 脚本示例 - 创建和管理由专用节点（虚拟机）组成的 Batch AI 群集
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ms.openlocfilehash: 10f3444f81dfaeac4331f0b7798ade7eefbd29fb
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058071"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>CLI 示例：创建和管理由专用节点组成的 Batch AI 群集

此脚本演示了 Azure CLI 中可用的一些命令，这些命令可创建和管理由专用节点（虚拟机）组成的 Batch AI 群集。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.38 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组以及与之关联的所有资源。

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | 创建 Batch AI 工作区。 |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | 创建 Batch AI 群集。 |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | 显示有关 Batch AI 群集的信息。 |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | 列出 Batch AI 群集中的节点。 |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | 重设 Batch AI 群集大小。  |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。
