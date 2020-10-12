---
title: 加密 Azure 自动化中的安全资产
description: 本文提供了将自动化帐户配置为使用加密的概念。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 7d59ca60c7f90c227885927086511bd1f8ac7ca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185834"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>加密 Azure 自动化中的安全资产

Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产在 Azure 自动化中受到了多级加密的保护。 根据用于加密的顶级密钥，有两种加密模型：

- 使用 Microsoft 托管密钥
- 使用你管理的密钥

## <a name="microsoft-managed-keys"></a>Microsoft 托管密钥

你的 Azure 自动化帐户默认使用 Microsoft 托管密钥。

每一安全资产是通过针对每个自动化帐户生成的唯一密钥（数据加密密钥）加密并存储在 Azure 自动化中的。 这些密钥本身是通过另一个唯一的密钥加密并存储在 Azure 自动化中的，该密钥是针对每个名为帐户加密密钥 (AEK) 的帐户生成的。 这些帐户加密密钥是通过 Microsoft 托管密钥加密并存储在 Azure 自动化中的。 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>通过 Key Vault 管理的密钥（预览版）

可以通过自己的密钥来管理自动化帐户的安全资产加密。 当你在自动化帐户级别指定客户管理的密钥时，此密钥用于保护和控制对自动化帐户的帐户加密密钥的访问。 这反过来用于加密和解密所有安全资产。 客户管理的密钥在创建、轮换、禁用和撤销访问控制方面可提供更大的灵活性。 此外，你还可以审核用于保护安全资产的加密密钥。

使用 Azure Key Vault 存储客户管理的密钥。 可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure 密钥保管库 API 来生成密钥。  有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>为自动化帐户使用客户管理的密钥

当你通过客户管理的密钥对自动化帐户使用加密时，Azure 自动化会使用相关密钥保管库中的客户管理的密钥来包装帐户加密密钥。 启用客户管理的密钥不会影响性能，并且帐户会立即通过新密钥受到加密，没有任何延迟。

新的自动化帐户始终是通过 Microsoft 托管密钥加密的。 创建帐户时不能启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略应向与自动化帐户关联的托管标识授予密钥权限。 托管标识仅在创建存储帐户后可用。

当你通过启用或禁用客户管理的密钥、更新密钥版本或者指定不同的密钥来修改用于 Azure 自动化安全资产加密的密钥时，帐户加密密钥的加密会更改，但不需要对 Azure 自动化帐户中的安全资产进行重新加密。

> [!NOTE] 
> 若要启用客户管理的密钥，需要使用 api version 2020-01-13-preview 来进行 Azure 自动化 REST API 调用

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自动化中使用客户管理的密钥的先决条件

为自动化帐户启用客户管理的密钥之前，必须先确保满足以下先决条件：

 - 客户管理的密钥存储在 Azure Key Vault 中。 
 - 同时在密钥保管库上启用“软删除”和“不清除”属性 。 在意外删除了密钥的情况下需要这些功能才能恢复密钥。
 - Azure 自动化加密仅支持 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。
- 自动化帐户和密钥保管库可以位于不同的订阅中，但它们需要位于同一个 Azure Active Directory 租户中。

### <a name="assignment-of-an-identity-to-the-automation-account"></a>将标识分配给自动化帐户

若要将客户管理的密钥用于自动化帐户，你的自动化帐户需要对存储客户管理的密钥的密钥保管库进行身份验证。 Azure 自动化使用系统分配的托管标识来对 Azure Key Vault 的帐户进行身份验证。 有关托管标识的详细信息，请参阅[什么是 Azure 资源托管标识？](../active-directory/managed-identities-azure-resources/overview.md)

使用以下 REST API 调用向自动化帐户配置系统分配的托管标识：

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

请求正文：

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

自动化帐户的系统分配的标识会在响应中返回，与以下内容类似：

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>配置 Key Vault 访问策略

将托管标识分配给自动化帐户后，可以配置对存储客户管理的密钥的密钥保管库的访问。 Azure 自动化需要对客户管理的密钥进行 get、recover、wrapKey 和 UnwrapKey 操作   。

可以使用以下 REST API 调用来设置此类访问策略：

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

请求正文：

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> 必须提供“tenantId”和“objectId”字段，它们的值分别是自动化帐户的托管标识响应中的“identity.tenantId”和“identity.principalId”   。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>将自动化帐户的配置更改为使用客户管理的密钥

最后，可以使用以下 REST API 调用将自动化帐户从 Microsoft 托管密钥切换为客户管理的密钥：

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

请求正文：

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

示例响应

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>轮换客户管理的密钥

可以根据你的符合性策略轮换 Azure Key Vault 中客户管理的密钥。 当密钥轮换时，必须将自动化帐户更新为使用新的密钥 URI。

轮换密钥不会触发对自动化帐户中安全资产的重新加密。 无需任何进一步操作。

## <a name="revocation-of-access-to-a-customer-managed-key"></a>撤销对客户管理的密钥的访问

若要撤销对客户管理的密钥的访问，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) 或 [Azure Key Vault CLI](/cli/azure/keyvault)。 撤销访问可有效阻止对自动化帐户中所有安全资产的访问，因为 Azure 自动化无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure Key Vault，请参阅[什么是 Azure Key Vault？](../key-vault/general/overview.md)
- 若要使用证书，请参阅[在 Azure 自动化中管理证书](shared-resources/certificates.md)。
- 若要处理凭据，请参阅[在 Azure 自动化中管理凭据](shared-resources/credentials.md)。
- 若要使用自动化变量，请参阅[在 Azure 自动化中管理变量](shared-resources/variables.md)。
- 有关使用连接的帮助，请参阅[在 Azure 自动化中管理连接](automation-connections.md)。
