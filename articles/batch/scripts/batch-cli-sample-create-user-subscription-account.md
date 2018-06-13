---
title: Azure CLI 脚本示例 - 创建 Batch 帐户 - 用户订阅 | Microsoft Docs
description: Azure CLI 脚本示例 - 在用户订阅模式下创建 Batch 帐户
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 0923512d62a1efe31e08377b6c074df2e0618552
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29848818"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI 示例：在用户订阅模式下创建 Batch 帐户

此脚本在用户订阅模式下创建 Azure Batch 帐户。 必须通过 Azure Active Directory 令牌对那些将计算节点分配到订阅中的帐户进行身份验证。 计算节点将计数分配到订阅的 vCPU（核心）配额。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az role assignment create](/cli/azure/role#az_role_assignment_create) | 为用户、组或服务主体创建新的角色分配。 |
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | 创建密钥保管库。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | 更新指定 Key Vault 的安全策略。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | 创建批处理帐户。  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
