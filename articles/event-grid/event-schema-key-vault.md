---
title: Azure Key Vault 的 Azure 事件网格事件架构
description: 介绍为 Azure 事件网格中的 Azure Key Vault 事件提供的属性和架构
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082868"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault 的 Azure 事件网格事件架构（预览）

本文提供了当前在预览版中[Azure Key Vault](../key-vault/index.yml)事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="available-event-types"></a>可用事件类型

Azure Key Vault 帐户会生成以下事件类型：

| 事件全名 | 事件显示名称 | 说明 |
| ---------- | ----------- |---|
| KeyVault. CertificateNewVersionCreated | 已创建证书新版本 | 创建新证书或新证书版本时触发。 |
| KeyVault. CertificateNearExpiry | 证书即将过期 | 当当前版本的证书即将过期时触发。 （默认值为到期日期前的30天。） |
| KeyVault. CertificateExpired | 证书已过期 | 证书过期时触发。 |
| KeyVault. KeyNewVersionCreated | 已创建密钥新版本 | 创建新的密钥或新密钥版本时触发。 |
| KeyVault. KeyNearExpiry | 密钥即将过期 | 当密钥的当前版本即将到期时触发。 （默认值为到期日期前的30天。） |
| KeyVault. KeyExpired | 密钥已过期 | 密钥到期时触发。 |
| KeyVault. SecretNewVersionCreated | 已创建机密新版本 | 创建新的机密或新的密钥版本时触发。 |
| KeyVault. SecretNearExpiry | 密码快要过期 | 当机密的当前版本即将到期时触发。 （默认值为到期日期前的30天。） |
| KeyVault. SecretExpired | 机密已过期 | 机密过期时触发。 |

## <a name="event-examples"></a>事件示例

下面的示例显示了**KeyVault. SecretNewVersionCreated**的架构：

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

## <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| ---------- | ----------- |---|
| id | 字符串 | 触发了此事件的对象的 ID |
| vaultName | 字符串 | 触发了此事件的对象的密钥保管库名称 |
| objectType | 字符串 | 触发了此事件的对象的类型 |
| objectName | 字符串 | 触发了此事件的对象的名称 |
| 版本 | 字符串 | 触发了此事件的对象的版本 |
| nbf | 数字 | 触发了此事件的对象自 1970-01-01T00：00：00Z 之后的非之前时间（秒） |
| exp | 数字 | 触发此事件的对象自 1970-01-01T00：00：00Z 之后的到期日期（以秒为单位） |


## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)。
* 有关如何创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 若要了解有关 Key Vault 与事件网格集成的详细信息，请参阅[通过 Azure 事件网格（预览版）监视 Key Vault](../key-vault/event-grid-overview.md)。
* 有关 Key Vault 与事件网格集成的教程，请参阅[使用 Azure 事件网格（预览）接收和响应 Key Vault 通知](../key-vault/event-grid-tutorial.md)。
* 若要获取有关 Key Vault 和 Azure 自动化的其他指南，请参阅：
    - [什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)
    - [通过 Azure 事件网格监视 Key Vault（预览版）](../key-vault/event-grid-overview.md)
    - [通过 Azure 事件网格（预览）接收和响应 key vault 通知](../key-vault/event-grid-tutorial.md)
    - [Azure 自动化概述](../automation/index.yml)
