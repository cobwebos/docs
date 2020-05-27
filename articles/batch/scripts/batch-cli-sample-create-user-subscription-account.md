---
title: Azure CLI 脚本示例 - 创建 Batch 帐户 - 用户订阅
description: 此脚本在用户订阅模式下创建 Azure Batch 帐户。 此帐户将计算节点分配到你的订阅。
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: 9f3919574dd7dcbe932c71dfcea2ac52ec906c6c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681888"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI 示例：在用户订阅模式下创建 Batch 帐户

此脚本在用户订阅模式下创建 Azure Batch 帐户。 必须通过 Azure Active Directory 令牌对那些将计算节点分配到订阅中的帐户进行身份验证。 计算节点将计数分配到订阅的 vCPU（核心）配额。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az role assignment create](/cli/azure/role) | 为用户、组或服务主体创建新的角色分配。 |
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-create) | 创建密钥保管库。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az-keyvault-set-policy) | 更新指定 Key Vault 的安全策略。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 创建批处理帐户。  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
