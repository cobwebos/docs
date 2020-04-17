---
title: 加密 Azure 自动化中的安全资产
description: Azure 自动化使用多级加密保护安全资产。 默认情况下，加密使用 Microsoft 管理的密钥完成。 客户可以将其自动化帐户配置为使用客户管理的密钥进行加密。 本文介绍了两种加密模式的详细信息以及如何在两种模式之间切换。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: a82d2b6d9521ba7dd5e7b194c26ff8fe5a100871
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457478"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>加密 Azure 自动化中的安全资产

Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产在 Azure 自动化中使用多级别的加密进行保护。 基于用于加密的顶级密钥，有两种加密模型：
-    使用 Microsoft 管理的密钥
-    使用客户管理的密钥

## <a name="microsoft-managed-keys"></a>微软管理的密钥

默认情况下，Azure 自动化帐户使用 Microsoft 管理的密钥。

每个安全资产都使用为每个自动化帐户生成的唯一密钥（数据加密密钥）加密并存储在 Azure 自动化中。 这些密钥本身使用为每个帐户生成称为帐户加密密钥 （AEK） 的唯一密钥在 Azure 自动化中加密并存储。 这些帐户加密密钥使用 Microsoft 管理的密钥加密并存储在 Azure 自动化中。 

## <a name="customer-managed-keys-with-key-vault-preview"></a>带密钥保管库的客户管理密钥（预览）

您可以使用自己的密钥管理自动化帐户的安全资产加密。 在自动化帐户级别指定客户管理的密钥时，该密钥用于保护和控制对自动化帐户帐户帐户加密密钥的访问。 这反过来又用于加密和解密所有安全资产。 使用客户管理的密钥可以更灵活地创建、轮换、禁用和撤销访问控制。 您还可以审核用于保护安全资产的加密密钥。

使用 Azure 密钥保管库存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。  有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>为自动化帐户启用客户管理的密钥

使用自动化帐户的客户托管密钥启用加密时，Azure 自动化使用关联的密钥保管库中的客户托管密钥包装帐户加密密钥。 启用客户管理的密钥不会影响性能，并且帐户将立即使用新密钥加密，不会有任何延迟。

新的自动化帐户始终使用 Microsoft 管理的密钥进行加密。 当创建帐户时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure 密钥保管库中，密钥保管库必须预配访问策略，这些策略向与自动化帐户关联的托管标识授予密钥权限。 托管标识仅在存储帐户创建后可用。

修改用于 Azure 自动化安全资产加密的密钥时，通过启用或禁用客户管理的密钥、更新密钥版本或指定其他密钥，帐户加密密钥的加密将更改，但 Azure 自动化帐户中的安全资产无需重新加密。

以下三节介绍了为自动化帐户启用客户管理密钥的机制。 

> [!NOTE] 
> 要启用客户管理的密钥，您需要使用 api 版本 2020-01-13 预览进行 Azure 自动化 REST API 调用

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自动化中使用客户管理密钥的先决条件

在为自动化帐户启用客户管理的密钥之前，必须确保满足以下先决条件：

 - 客户管理密钥存储在 Azure 密钥保管库中。 
 - 在密钥保管库中启用 **"软删除****"和"不清除"** 属性。 这些功能是必需的，以便在意外删除时恢复密钥。
 - Azure 自动化加密仅支持 RSA 密钥。 有关密钥的详细信息，请参阅有关[Azure 密钥保管库密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)。
- 自动化帐户和密钥保管库可以位于不同的订阅中，但需要位于同一 Azure 活动目录租户中。

### <a name="assign-an-identity-to-the-automation-account"></a>将标识分配给自动化帐户

要将客户管理的密钥与自动化帐户一起使用，自动化帐户需要针对存储客户管理的密钥的密钥保管库进行身份验证。 Azure 自动化使用系统分配的托管标识使用 Azure 密钥保管库对帐户进行身份验证。 有关托管标识的详细信息，请参阅[什么是 Azure 资源的托管标识？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

使用以下 REST API 调用将托管标识分配给自动化帐户的系统：

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

自动化帐户的系统分配标识在响应中返回，类似于以下内容：

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

### <a name="configure-the-key-vault-access-policy"></a>配置密钥保管库访问策略

将托管标识分配给自动化帐户后，将配置对存储客户托管密钥的密钥保管库的访问。 Azure 自动化要求在客户管理的密钥上**获取**、**恢复**、**包装键**、**解包密钥**。

此类访问策略可以使用以下 REST API 调用进行设置：

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
> **租户 Id**和**objectId**字段必须分别从自动化帐户的托管标识响应中提供**标识.租户Id**和**标识.theid 的值**。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>更改自动化帐户的配置以使用客户管理的密钥

最后，您可以使用以下 REST API 调用将自动化帐户从 Microsft 管理的密钥切换到客户管理的密钥：

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

## <a name="manage-customer-managed-keys-lifecycle"></a>管理客户管理密钥生命周期

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 旋转密钥时，必须更新自动化帐户才能使用新的密钥 URI。

旋转密钥不会触发对自动化帐户中的安全资产进行重新加密。 无需采取进一步行动。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

要撤消对客户管理密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) 或 [Azure 密钥保管库 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 由于 Azure 自动化无法访问加密密钥，因此撤消访问可有效地阻止对自动化帐户中的所有安全资产的访问。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Key Vault？](../key-vault/general/overview.md)

- [Azure 自动化中的证书资产](shared-resources/certificates.md)

- [Azure 自动化中的凭据资产](shared-resources/credentials.md)

- [Azure 自动化中的变量资产](shared-resources/variables.md)
