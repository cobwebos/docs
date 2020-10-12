---
title: '用 Azure Key Vault 托管 HSM (预览版中存储的客户托管密钥配置加密) '
titleSuffix: Azure Storage
description: 了解如何使用 Azure CLI 为存储在 Azure Key Vault 托管 HSM (预览版) 中的客户托管密钥配置 Azure 存储加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9c742ca2fd9779589a3c8aea7f030460c5db8b5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994136"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>用 Azure Key Vault 托管 HSM (预览版中存储的客户托管密钥配置加密) 

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了进一步控制加密密钥，你可以管理自己的密钥。 客户托管的密钥必须存储在 Azure Key Vault 或 Key Vault 托管硬件安全模型 (HSM)  (预览版) 中。 Azure Key Vault 托管 HSM 是经过 FIPS 140-2 第3级验证的 HSM。

本文介绍如何使用 Azure CLI 通过存储在托管 HSM 中的客户托管密钥配置加密。 若要了解如何使用存储在密钥保管库中的客户托管密钥配置加密，请参阅 [使用 Azure Key Vault 中存储的客户托管密钥配置加密](customer-managed-keys-configure-key-vault.md)。

> [!NOTE]
> Azure Key Vault 和 Azure Key Vault 托管 HSM 支持相同的 Api 和管理接口进行配置。

## <a name="assign-an-identity-to-the-storage-account"></a>将标识分配到存储帐户

首先，将系统分配的托管标识分配给存储帐户。 你将使用此托管标识向存储帐户授予访问托管 HSM 的权限。 有关系统分配的托管标识的详细信息，请参阅 [Azure 资源的托管标识是什么？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 Azure CLI 分配托管标识，请调用 [az storage account update](/cli/azure/storage/account#az-storage-account-update)。 请记住，用自己的值替换括号中的占位符值：

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>将角色分配给存储帐户以访问托管 HSM

接下来，将 **托管 Hsm 加密服务加密** 角色分配给存储帐户的托管标识，以便存储帐户具有托管 hsm 的权限。 Microsoft 建议将角色分配的范围限定为单个密钥级别，以便向托管标识授予尽可能少的权限。

若要为存储帐户创建角色分配，请调用 [az key vault role 分配 create](/cli/azure/role/assignment#az_role_assignment_create)。 请记得将括号中的占位符值替换为你自己的值。
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>使用托管 HSM 中的密钥配置加密

最后，使用客户管理的密钥配置 Azure 存储加密，以使用存储在托管 HSM 中的密钥。 支持的密钥类型包括 RSA-HSM 密钥，大小为2048、3072和4096。 安装 Azure CLI 2.12.0 或更高版本，以便在托管 HSM 中将加密配置为使用客户管理的密钥。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要自动更新客户管理的密钥的密钥版本，请在使用存储帐户的客户管理的密钥配置加密时省略密钥版本。 请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)，以便更新存储帐户的加密设置，如以下示例所示。 包含 `--encryption-key-source parameter` 并将其设置为， `Microsoft.Keyvault` 以便为帐户启用客户管理的密钥。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

若要手动更新客户管理的密钥的版本，请在配置存储帐户的加密时包含密钥版本：

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

手动更新密钥版本时，需要更新存储帐户的加密设置以使用新版本。 首先，通过调用 [az keyvault show](/cli/azure/keyvault#az-keyvault-show) 查询 Key Vault URI，并通过调用 [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions) 查询密钥版本。 然后调用 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 来更新存储帐户的加密设置，以使用新的密钥版本，如上一示例所示。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户托管密钥](customer-managed-keys-overview.md)