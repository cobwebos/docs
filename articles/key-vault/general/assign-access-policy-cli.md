---
title: " (CLI 分配 Azure Key Vault 访问策略) "
description: 如何使用 Azure CLI 将 Key Vault 访问策略分配到服务主体或应用程序标识。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 32a323e8cc56a8c45b18737bb55ebe19d4be27c0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380696"
---
# <a name="assign-a-key-vault-access-policy"></a>分配 Key Vault 访问策略

Key Vault 访问策略确定给定的服务主体（即应用程序或用户组）是否可以对 Key Vault 的 [机密](../secrets/index.yml)、 [密钥](../keys/index.yml)和 [证书](../certificates/index.yml)执行不同的操作。 您可以使用 [Azure 门户](assign-access-policy-portal.md)、本文 () 或 [Azure PowerShell](assign-access-policy-powershell.md)的 Azure CLI 分配访问策略。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

有关使用 Azure CLI 在 Azure Active Directory 中创建组的详细信息，请参阅 [az ad group create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) 和 [az ad group member add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)。

## <a name="configure-the-azure-cli-and-sign-in"></a>配置 Azure CLI 并登录

1. 若要在本地运行 Azure CLI 命令，请安装 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
 
    若要直接在云中运行命令，请使用 [Azure Cloud Shell](/azure/cloud-shell/overview)。

1. 仅本地 CLI：使用以下项登录 Azure `az login` ：

    ```bash
    az login
    ```

    此 `az login` 命令将打开一个浏览器窗口，以便在需要时收集凭据。

## <a name="acquire-the-object-id"></a>获取对象 ID

确定要为其分配访问策略的应用程序、组或用户的对象 ID：

- 应用程序和其他服务主体：使用 [az ad sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) 命令检索你的服务主体。 检查命令的输出，以确定要将访问策略分配到的安全主体的对象 ID。

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 组：使用 [az ad group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) 命令，使用参数筛选结果 `--display-name` ：

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 用户：使用 [az ad user show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) 命令，并在参数中传递用户的电子邮件地址 `--id` ：

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>分配访问策略
    
使用 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令分配所需的权限：

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

将替换 `<object-id>` 为服务主体的对象 ID。

仅在 `--secret-permissions` `--key-permissions` `--certificate-permissions` 向这些特定类型分配权限时，才需要包括、和。 `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [Az keyvault set 策略](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)文档中提供了、和的允许值。

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性：标识和访问管理](overview-security.md#identity-and-access-management)
- [保护 Key Vault](secure-your-key-vault.md)
- [Azure Key Vault 开发人员指南](developers-guide.md)
- [Azure Key Vault 最佳做法](best-practices.md)
