---
title: 通过 Azure 事件网格监视 Key Vault
description: 使用 Azure 事件网格订阅 Key Vault 事件
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033493"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>通过 Azure 事件网格监视 Key Vault （预览版）

与事件网格的 Key Vault 集成目前处于预览阶段。 它允许用户在密钥保管库中存储的机密状态发生更改时收到通知。 状态更改定义为即将过期的机密（在30天内到期）、已过期的机密或具有新版本的机密。 支持所有三种机密类型（密钥、证书和机密）的通知。

应用程序可以使用新式无服务器体系结构对这些事件做出反应，而不需要复杂的代码，也无需昂贵和低效的轮询服务。 事件将通过[Azure 事件网格](https://azure.microsoft.com/services/event-grid/)推送到事件处理程序（例如[Azure Functions](https://azure.microsoft.com/services/functions/)、 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)，甚至是你自己的 Webhook），只需为你使用的部分付费。 有关定价的详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="key-vault-events-and-schemas"></a>Key Vault 事件和架构

事件网格使用[事件订阅](../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Key Vault 事件包含响应数据更改所需的所有信息。 由于事件属性以 "KeyVault" 开头，因此可以识别 Key Vault 事件。

有关详细信息，请参阅[Key Vault 事件架构](../event-grid/event-schema-key-vault.md)。

> [!NOTE]
> 仅在设置了订阅后创建的机密版本（所有三个类型）触发事件。
>
> 对于现有的机密，你必须生成新的版本。

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理 Key Vault 事件的应用程序应遵循以下建议做法：

* 可以配置多个订阅，将事件路由至同一事件处理程序。 不要假设事件来自特定源，但要检查消息的主题，以确保它来自于你所期望的密钥保管库。
* 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
* 忽略不了解的字段。  此做法有助于适应将来可能添加的新功能。
* 使用“subject”前缀和后缀匹配项，将事件限制为特定事件。

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 概述](key-vault-overview.md)
- [Azure 事件网格概述](../event-grid/overview.md)
- [如何：将 Key Vault 事件路由到自动化 Runbook （预览版）](event-grid-tutorial.md)。
- [Azure Key Vault 的 Azure 事件网格事件架构（预览）](../event-grid/event-schema-key-vault.md)
- [Azure 自动化概述](../automation/index.yml)
