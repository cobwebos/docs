---
title: Azure CLI 脚本示例 - 使用配置文件创建 Batch AI 群集 | Microsoft Docs
description: Azure CLI 脚本示例 - 通过在 JSON 文件中指定配置设置创建 Batch AI 群集。
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
ms.date: 08/16/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 41a3a801214ff00c01397034e26fde6946ab97f0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407807"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>CLI 示例：使用群集配置文件创建 Batch AI 群集

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

此脚本展示了如何使用 JSON 配置文件指定 Batch AI 群集的设置。 请为 `az batchai cluster create` 使用这些设置而非使用相应的命令行参数。 当需要在群集节点上装载多个文件系统或者希望在多个群集中使用相同的配置时，配置文件非常有用。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 CLI，则请注意，本指南要求运行 Azure CLI 2.0.38 或更高版本。 运行 `az --version` 即可确定版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组以及与之关联的所有资源。

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | 在存储帐户中创建文件共享。 |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | 创建 Batch AI 工作区。 |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | 创建 Batch AI 群集。 |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | 显示有关 Batch AI 群集的信息。 |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。
