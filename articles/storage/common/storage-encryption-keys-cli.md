---
title: 使用 Azure CLI 来配置客户管理的密钥
titleSuffix: Azure Storage
description: 了解如何使用 Azure CLI 通过 Azure Key Vault Azure 存储加密来配置客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤消访问控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9b9ec315954f5916339bb006cb020acc28886839
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895324"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>使用 Azure CLI 配置客户管理的密钥 Azure Key Vault

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文说明如何使用 Azure CLI 配置使用客户管理的密钥的 Azure Key Vault。 若要了解如何使用 Azure CLI 创建密钥保管库，请参阅[快速入门：使用 Azure CLI 从 Azure Key Vault 设置和检索机密](../../key-vault/quick-create-cli.md)。

> [!IMPORTANT]
> 将客户托管的密钥用于 Azure 存储加密要求在 key vault 上设置两个属性，**软删除**并不**清除**。 默认情况下不启用这些属性。 若要启用这些属性，请使用 PowerShell 或 Azure CLI。
> 仅支持 RSA 密钥和密钥大小2048。

## <a name="assign-an-identity-to-the-storage-account"></a>为存储帐户分配标识

若要为你的存储帐户启用客户管理的密钥，请首先将系统分配的托管标识分配给存储帐户。 你将使用此托管标识向存储帐户授予访问密钥保管库的权限。

若要使用 Azure CLI 分配托管标识，请调用[az storage account update](/cli/azure/storage/account#az-storage-account-update)。 请记得将占位符值替换为您自己的值。

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

有关使用 Azure CLI 配置系统分配的托管标识的详细信息，请参阅[使用 Azure CLI 在 AZURE VM 上配置 azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)。

## <a name="create-a-new-key-vault"></a>创建新的密钥保管库

用于存储 Azure 存储加密的客户托管密钥的密钥保管库必须启用两个密钥保护设置，**软删除**并不**清除**。 若要在启用了这些设置的情况下使用 PowerShell 或 Azure CLI 创建新的密钥保管库，请执行以下命令。 请记得将占位符值替换为您自己的值。 

若要使用 Azure CLI 创建新的密钥保管库，请调用[az keyvault create](/cli/azure/keyvault#az-keyvault-create)。 请记得将占位符值替换为您自己的值。

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>配置密钥保管库访问策略

接下来，为密钥保管库配置访问策略，以便存储帐户有权访问它。 在此步骤中，你将使用之前分配给存储帐户的托管标识。

若要设置密钥保管库的访问策略，请调用[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)。 请记得将占位符值替换为您自己的值。

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>新建密钥

接下来，在密钥保管库中创建密钥。 若要创建密钥，请调用[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)。 请记得将占位符值替换为您自己的值。

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>通过客户托管的密钥配置加密

默认情况下，Azure 存储加密使用 Microsoft 托管密钥。 为客户管理的密钥配置 Azure 存储帐户，并指定与存储帐户关联的密钥。

若要更新存储帐户的加密设置，请调用[az storage account update](/cli/azure/storage/account#az-storage-account-update)。 此示例还查询密钥保管库 URI 和最新密钥版本，这两个值都是将密钥与存储帐户相关联所需要的。 请记得将占位符值替换为您自己的值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>更新密钥版本

当你创建新的密钥版本时，你将需要更新存储帐户以使用新版本。 首先，通过调用 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)来查询 KEY vault URI，并通过调用[az keyvault key list 版本](/cli/azure/keyvault/key#az-keyvault-key-list-versions)来查询密钥版本。 然后调用[az storage account update](/cli/azure/storage/account#az-storage-account-update) ，将存储帐户的加密设置更新为使用新的密钥版本，如前一部分中所示。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md) 
- [什么是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
