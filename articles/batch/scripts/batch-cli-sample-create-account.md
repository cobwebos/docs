---
title: Azure CLI 脚本示例 - 创建 Batch 帐户 - Batch 服务
description: 此脚本在 Batch 服务模式下创建 Azure Batch 帐户，并演示如何查询或更新该帐户的各个属性。
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 5fce745340c583b902c98716678a9b7afcfc002c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681927"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI 示例：在 Batch 服务模式下创建 Batch 帐户

此脚本在 Batch 服务模式下创建 Azure Batch 帐户，并演示如何查询或更新该帐户的各个属性。 在默认 Batch 服务模式下创建 Batch 帐户时，其计算节点由 Batch 服务在内部分配。 分配的计算节点受到单独的 vCPU（核心）配额的限制。可以通过共享密钥凭据或 Azure Active Directory 令牌对帐户进行身份验证。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 创建批处理帐户。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | 更新批处理帐户的属性。  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | 检索指定批处理帐户的详细信息。  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | 检索指定批处理帐户的访问密钥。  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
