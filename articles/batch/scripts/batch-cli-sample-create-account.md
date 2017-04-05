---
title: "Azure CLI 脚本示例 - 创建批处理帐户 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 创建批处理帐户"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4d37dfc6c7110aa32788bbd3c3f81cd84a1426d2
ms.lasthandoff: 03/24/2017

---

# <a name="create-a-batch-account-with-the-azure-cli"></a>使用 Azure CLI 创建批处理帐户

此脚本创建 Azure 批处理帐户，并演示如何查询和更新该帐户的各个属性。

如果需要，请使用 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)中的说明安装 Azure CLI，然后运行 `az login` 登录到 Azure。

## <a name="batch-account-sample-script"></a>批处理帐户示例脚本

创建批处理帐户时，默认情况下，其计算节点由批处理服务在内部分配。 分配的计算节点受到单独的核心配额的限制。可以通过共享密钥凭据或 Azure Active Dirctory 令牌对帐户进行身份验证。

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account.sh "创建帐户")]

## <a name="batch-account-using-user-subscription-sample-script"></a>使用用户订阅创建批处理帐户的示例脚本

也可以选择让批处理在你自己的 Azure 订阅中创建其计算节点。
用于将计算节点分配到订阅的帐户必须通过 Azure Active Directory 令牌进行身份验证，分配的计算节点数将计入订阅配额。 若要在此模式下创建帐户，必须在创建帐户时指定 Key Vault 引用。

[!code-azurecli[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh  "使用用户订阅创建帐户")]

## <a name="clean-up-deployment"></a>清理部署

运行上述任一示例脚本后，请运行以下命令以删除资源组和所有相关的资源（包括批处理帐户、Azure 存储帐户和 Azure Key Vault）。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、批处理帐户和所有相关资源。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az batch account create](https://docs.microsoft.com/cli/azure/batch/account#create) | 创建批处理帐户。  |
| [az batch account set](https://docs.microsoft.com/cli/azure/batch/account#set) | 更新批处理帐户的属性。  |
| [az batch account show](https://docs.microsoft.com/cli/azure/batch/account#show) | 检索指定批处理帐户的详细信息。  |
| [az batch account keys list](https://docs.microsoft.com/cli/azure/batch/account/keys#list) | 检索指定批处理帐户的访问密钥。  |
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 创建存储帐户。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | 创建密钥保管库。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | 更新指定 Key Vault 的安全策略。 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure 批处理 CLI 文档](../batch-cli-samples.md)中找到其他批处理 CLI 脚本示例。

