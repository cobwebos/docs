---
title: 自动化中的安全资产加密
description: Azure 自动化使用多个加密级别保护安全资产。 默认情况下，使用 Microsoft 托管的密钥进行加密。 客户可以将其自动化帐户配置为使用客户托管的密钥进行加密。 本文介绍两种方法的详细信息，以及如何在这两种模式之间进行切换。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: fa8ea40d827807565e71d1e790c8c52986b85ec8
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904949"
---
# <a name="secure-assets-in-azure-automation"></a>Azure 自动化中的安全资产

Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产在 Azure 自动化中使用多个加密级别进行保护。 根据用于加密的顶级密钥，有两种加密模型：
-   使用 Microsoft 托管密钥
-   使用客户托管的密钥

### <a name="microsoft-managed-keys"></a>Microsoft 托管的密钥

默认情况下，你的 Azure 自动化帐户使用 Microsoft 托管密钥。

每个安全资产均使用为每个自动化帐户生成的唯一密钥（数据加密密钥）进行加密并存储在 Azure 自动化中。 这些密钥本身使用另一个唯一密钥加密并存储在 Azure 自动化中，该密钥是为每个称为帐户加密密钥（AEK）的帐户生成的。 这些帐户加密密钥使用 Microsoft 托管密钥加密并存储在 Azure 自动化中。 

### <a name="customer-managed-keys-with-key-vault-preview"></a>客户管理的密钥与 Key Vault （预览版）

你可以使用自己的密钥管理 Azure 自动化中的安全资产加密。 在自动化帐户级别指定客户托管的密钥时，该密钥用于保护和控制对自动化帐户的帐户加密密钥的访问权限，而该密钥反过来用于加密和解密所有安全资产。 客户管理的密钥提供更大的灵活性，以创建、轮换、禁用和撤消访问控制。 你还可以审核用于保护安全资产的加密密钥。 

必须使用 Azure Key Vault 来存储客户管理的密钥。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。  有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>为自动化帐户启用客户管理的密钥

使用自动化帐户的客户托管密钥启用加密时，Azure 自动化会使用关联的密钥保管库中客户托管的密钥包装帐户加密密钥。 启用客户管理的密钥不会影响性能，并且会立即用新密钥加密帐户，而不会有任何时间延迟。

新的自动化帐户始终使用 Microsoft 托管的密钥进行加密。 创建帐户时，不能启用客户管理的密钥。 客户托管的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与自动化帐户关联的托管标识授予密钥权限。 托管标识仅在创建存储帐户后可用。

当你通过启用或禁用客户管理的密钥、更新密钥版本或指定其他密钥来修改用于 Azure 自动化的密钥时，将更改根密钥，但会更改 Azure 中的安全资产，自动化帐户无需重新加密。

以下三个部分介绍为自动化帐户启用客户管理的密钥的机制。 

> [!NOTE] 
> 若要启用客户管理的密钥，当前需要使用 api 版本 2020-01-13-预览版创建 Azure 自动化 REST Api

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自动化中使用客户托管密钥的先决条件

为自动化帐户启用客户托管密钥之前，必须确保满足以下先决条件

 - 托管密钥存储在 Azure Key Vault 中。 
 - 必须同时启用**软删除**和不**清除**密钥保管库中的属性。 需要这些功能才能在意外删除的情况下恢复密钥。
 - Azure 自动化加密仅支持 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)。
- 自动化帐户和密钥保管库可以位于不同的订阅中，但需要位于同一个 Azure Active Directory 租户中。

### <a name="assign-an-identity-to-the-automation-account"></a>将标识分配给自动化帐户

若要将客户托管密钥与自动化帐户一起使用，你的自动化帐户需要针对存储客户管理的密钥的 keyvault 进行身份验证。 Azure Automation 使用系统分配的托管标识，通过 Key Vault 对帐户进行身份验证。 有关托管标识的详细信息，请参阅[什么是 Azure 资源的托管标识？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

使用以下 REST API 调用为自动化帐户配置系统分配的托管标识

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
请求正文
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```    

在响应中返回自动化帐户的系统分配的标识

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

将托管标识分配到自动化帐户后，你可以配置对存储客户托管密钥的 Key Vault 的访问。 Azure Automation 需要对客户托管密钥的**get**、 **recover**、 **wrapKey**、 **UnwrapKey** 。

可以使用以下 REST API 调用来设置此类访问策略。

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```
请求正文

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
> TenantId 和 objectId 字段必须与 automation 帐户的托管标识响应的 principalId 和 identity 值一起提供。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>将自动化帐户的配置更改为使用客户托管密钥

最后，你可以使用以下 REST API 调用将自动化帐户从 Microsft 管理的密钥切换到客户管理的密钥。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
请求正文

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

你可以根据你的符合性策略，在 Azure Key Vault 中轮替客户管理的密钥。 当密钥旋转时，必须将自动化帐户更新为使用新的密钥 URI。 

轮换密钥不会触发存储帐户中数据的重新加密。 用户无需执行任何其他操作。

## <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 有效地吊销访问权限会阻止对存储帐户中所有数据的访问，因为 Azure 存储无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md) 
- [Azure 自动化中的证书资产](shared-resources/certificates.md)
- [Azure 自动化中的凭据资产](shared-resources/credentials.md)
- [Azure 自动化中的变量资产](shared-resources/variables.md)
