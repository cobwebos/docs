---
title: 加密 Azure 自动化中的安全资产
description: Azure 自动化使用多个加密级别保护安全资产。 默认情况下，加密是使用 Microsoft 托管密钥实现的。 客户可将其自动化帐户配置为使用客户管理的密钥进行加密。 本文将详细介绍这两种加密模式，以及如何在两者之间进行切换。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604819"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>加密 Azure 自动化中的安全资产

Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产在 Azure 自动化中通过多个加密级别受到保护。 根据用于加密的顶级密钥，可通过两种加密模型实现加密：
-    使用 Microsoft 托管密钥
-    使用客户管理的密钥

## <a name="microsoft-managed-keys"></a>Microsoft 托管密钥

Azure 自动化帐户默认使用 Microsoft 托管密钥。

每个安全资产已通过针对每个自动化帐户生成的唯一密钥（数据加密密钥）进行加密并存储在 Azure 自动化中。 这些密钥本身已通过针对每个帐户生成的另一个唯一密钥（称为帐户加密密钥 (AEK)）进行加密并存储在 Azure 自动化中。 这些帐户加密密钥已通过 Microsoft 托管密钥进行加密并存储在 Azure 自动化中。 

## <a name="customer-managed-keys-with-key-vault-preview"></a>客户管理的密钥与 Key Vault（预览版）

可以使用自己的密钥来管理自动化帐户的安全资产加密。 在自动化帐户级别指定客户管理的密钥时，该密钥将用于保护和控制对自动化帐户的帐户加密密钥的访问。 而帐户加密密钥又可用于加密和解密所有安全资产。 使用客户管理的密钥可以更灵活地创建、轮换、禁用和撤销访问控制。 还可以审核用于保护安全资产的加密密钥。

使用 Azure Key Vault 存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。  有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>为自动化帐户启用客户管理的密钥

为自动化帐户启用使用客户管理的密钥进行加密时，Azure 自动化会使用关联的 Key Vault 中客户管理的密钥包装帐户加密密钥。 启用客户管理的密钥不会影响性能，会立即使用新密钥对帐户加密，且不会出现任何延迟。

新自动化帐户始终使用 Microsoft 托管密钥进行加密。 当创建帐户时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，必须使用访问策略对 Key Vault 进行预配，这些策略将密钥权限授予与自动化帐户关联的托管标识。 托管标识仅在存储帐户创建后可用。

通过启用或禁用客户管理的密钥、更新密钥版本或指定其他密钥来修改用于 Azure 自动化安全资产加密的密钥时，帐户加密密钥的加密会更改，但 Azure 自动化帐户中的安全资产不需要重新加密。

以下三个部分将介绍为自动化帐户启用客户管理的密钥的机制。 

> [!NOTE] 
> 若要启用客户管理的密钥，需使用 API 版本 2020-01-13-preview 发出 Azure 自动化 REST API 调用

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自动化中使用客户管理的密钥的先决条件

在为自动化帐户启用客户管理的密钥之前，必须确保满足以下先决条件：

 - 客户管理的密钥存储在 Azure Key Vault 中。 
 - 在 Key Vault 中同时启用“软删除”和“不清除”属性。   需要启用这些功能，才能在意外删除密钥的情况下恢复密钥。
 - Azure 自动化加密仅支持 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)。
- 自动化帐户和 Key Vault 可以位于不同的订阅中，但需要位于同一个 Azure Active Directory 租户中。

### <a name="assign-an-identity-to-the-automation-account"></a>将标识分配给自动化帐户

若要对自动化帐户使用客户管理的密钥，自动化帐户需要针对存储客户管理的密钥的 Key Vault 进行身份验证。 Azure 自动化使用系统分配的托管标识对采用 Azure Key Vault 的帐户进行身份验证。 有关托管标识的详细信息，请参阅[Azure 资源的托管标识是什么？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

使用以下 REST API 调用为自动化帐户配置系统分配的托管标识：

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

自动化帐户的系统分配标识将在如下所示的响应中返回：

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

### <a name="configure-the-key-vault-access-policy"></a>配置 Key Vault 访问策略

将托管标识分配到自动化帐户后，请配置对存储客户管理的密钥的 Key Vault 的访问权限。 Azure 自动化需要对客户管理的密钥拥有 get、recover、wrapKey 和 UnwrapKey 权限。    

可以使用以下 REST API 调用设置此类访问策略：

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
> **Tenantid**和**objectId**字段必须分别与 Automation 帐户的托管标识的响应一起提供**principalId 和 identity** **值。**

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>将自动化帐户的配置更改为使用客户托管的密钥

最后，你可以使用以下 REST API 调用将自动化帐户从 Microsoft 托管的密钥切换到客户管理的密钥：

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

## <a name="manage-customer-managed-keys-lifecycle"></a>管理客户管理的密钥生命周期

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 当密钥旋转时，必须将自动化帐户更新为使用新的密钥 URI。

轮换密钥不会触发自动化帐户中安全资产的重新加密。 无需执行其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 有效地吊销访问权限会阻止访问自动化帐户中的所有安全资产，因为 Azure 自动化无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault？](../key-vault/general/overview.md)
- [Azure 自动化中的证书资产](shared-resources/certificates.md)
- [Azure 自动化中的凭据资产](shared-resources/credentials.md)
- [Azure 自动化中的变量资产](shared-resources/variables.md)
