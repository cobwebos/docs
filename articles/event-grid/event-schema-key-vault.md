---
title: Azure 密钥保管库作为事件网格源
description: 介绍针对 Azure 事件网格中的 Azure Key Vault 事件提供的属性和架构
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 36b7b81a18c8725929ab5676b844e1ee319e287f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393314"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure 密钥保管库作为事件网格源

本文提供了 [Azure Key Vault](../key-vault/index.yml)（当前为预览版）中事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

Azure Key Vault 帐户生成以下事件类型：

| 事件全名 | 事件显示名称 | 说明 |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | 创建的证书新版本 | 创建新证书或新证书版本时触发。 |
| Microsoft.KeyVault.CertificateNearExpiry | 证书即将过期 | 当前版本的证书即将过期时触发。 （事件在到期日期前 30 天触发。 |
| Microsoft.KeyVault.CertificateExpired | 证书已过期 | 证书过期时触发。 |
| Microsoft.KeyVault.KeyNewVersionCreated | 创建的密钥新版本 | 创建新密钥或新密钥版本时触发。 |
| Microsoft.KeyVault.KeyNearExpiry | 密钥即将过期 | 当前版本的密钥即将过期时触发。 （事件在到期日期前 30 天触发。 |
| Microsoft.KeyVault.KeyExpired | 密钥已过期 | 密钥过期时触发。 |
| Microsoft.KeyVault.SecretNewVersionCreated | 创建的机密新版本 | 创建新机密或新机密版本时触发。 |
| Microsoft.KeyVault.SecretNearExpiry | 机密即将过期 | 当前版本的机密即将过期时触发。 （事件在到期日期前 30 天触发。 |
| Microsoft.KeyVault.SecretExpired | 机密已过期 | 机密过期时触发。 |

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

| properties | 类型 | 说明 |
| ---------- | ----------- |---|
| id | 字符串 | 触发了此事件的对象的 ID |
| vaultName | 字符串 | 触发了此事件的对象的密钥保管库名称 |
| objectType | 字符串 | 触发了此事件的对象的类型 |
| objectName | 字符串 | 触发了此事件的对象的名称 |
| 版本 | 字符串 | 触发了此事件的对象的版本 |
| nbf | 数字 | 触发了此事件的对象的 not-before 日期（自 1970-01-01T00:00:00Z 以来的秒数） |
| exp | 数字 | 触发了此事件的对象的到期日期（自 1970-01-01T00:00:00Z 以来的秒数） |

## <a name="tutorials-and-how-tos"></a>教程和如何
|标题  |说明  |
|---------|---------|
| [通过 Azure 事件网格监视 Key Vault 事件](../key-vault/event-grid-overview.md) | 概述 Key Vault 与事件网格的集成。 |
| [教程：使用事件网格创建和监视密钥保管库事件](../key-vault/event-grid-tutorial.md) | 了解如何为 Key Vault 设置事件网格通知。 |


## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)。
* 有关如何创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 若要详细了解 Key Vault 与事件网格的集成，请参阅[使用 Azure 事件网格（预览版）监视 Key Vault](../key-vault/event-grid-overview.md)。
* 有关 Key Vault 与事件网格集成的教程，请参阅[通过 Azure 事件网格接收和响应 Key Vault 通知（预览版）](../key-vault/event-grid-tutorial.md)。
* 若要获取有关 Key Vault 和 Azure 自动化的其他指南，请参阅：
    - [什么是 Azure Key Vault？](../key-vault/key-vault-overview.md)
    - [通过 Azure 事件网格监视 Key Vault（预览版）](../key-vault/event-grid-overview.md)
    - [通过 Azure 事件网格接收和响应 Key Vault 通知（预览版）](../key-vault/event-grid-tutorial.md)
    - [Azure 自动化概述](../automation/index.yml)
