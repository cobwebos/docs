---
title: 充当事件网格源的 Azure Key Vault
description: 介绍针对 Azure 事件网格中的 Azure Key Vault 事件提供的属性和架构
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b3d961228429c1f84e45c1df9147fa1687ab5074
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324070"
---
# <a name="azure-key-vault-as-event-grid-source"></a>充当事件网格源的 Azure Key Vault

本文提供 [Azure Key Vault](../key-vault/index.yml)中事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

Azure Key Vault 帐户生成以下事件类型：

| 事件全名 | 事件显示名称 | 说明 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | 创建的证书新版本 | 创建新证书或新证书版本时触发。 |
| Microsoft.KeyVault.CertificateNearExpiry | 证书即将过期 | 当前版本的证书即将过期时触发。 （此事件在证书到期日期前 30 天触发。） |
| Microsoft.KeyVault.CertificateExpired | 证书已过期 | 证书过期时触发。 |
| Microsoft.KeyVault.KeyNewVersionCreated | 创建的密钥新版本 | 创建新密钥或新密钥版本时触发。 |
| Microsoft.KeyVault.KeyNearExpiry | 密钥即将过期 | 当前版本的密钥即将过期时触发。 （此事件在证书到期日期前 30 天触发。） |
| Microsoft.KeyVault.KeyExpired | 密钥已过期 | 密钥过期时触发。 |
| Microsoft.KeyVault.SecretNewVersionCreated | 创建的机密新版本 | 创建新机密或新机密版本时触发。 |
| Microsoft.KeyVault.SecretNearExpiry | 机密即将过期 | 当前版本的机密即将过期时触发。 （此事件在机密到期日期前 30 天触发。） |
| Microsoft.KeyVault.SecretExpired | 机密已过期 | 机密过期时触发。 |
| KeyVault. VaultAccessPolicyChanged | 保管库访问策略已更改 | 当 Key Vault 上的访问策略发生更改时触发。 它包括在将 Key Vault 权限模型更改为 Azure RBAC 或从 Azure RBAC 更改时的方案  |

### <a name="event-examples"></a>事件示例

以下示例显示 **Microsoft.KeyVault.SecretNewVersionCreated** 的架构：

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| ---------- | ----------- |---|
| id | string | 触发了此事件的对象的 ID |
| vaultName | string | 触发了此事件的对象的密钥保管库名称 |
| objectType | string | 触发了此事件的对象的类型 |
| objectName | string | 触发了此事件的对象的名称 |
| 版本 | string | 触发了此事件的对象的版本 |
| nbf | number | 触发了此事件的对象的 not-before 日期（自 1970-01-01T00:00:00Z 以来的秒数） |
| exp | number | 触发了此事件的对象的到期日期（自 1970-01-01T00:00:00Z 以来的秒数） |

## <a name="tutorials-and-how-tos"></a>教程和操作指南
|标题  |说明  |
|---------|---------|
| [通过 Azure 事件网格监视 Key Vault 事件](../key-vault/general/event-grid-overview.md) | 概述 Key Vault 与事件网格的集成。 |
| [教程：用事件网格创建和监视 Key Vault 事件](../key-vault/general/event-grid-logicapps.md) | 了解如何为 Key Vault 设置事件网格通知。 |


## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)。
* 有关如何创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关密钥保管库的详细信息，请参阅 [什么是 Azure Key Vault？](../key-vault/general/overview.md)

