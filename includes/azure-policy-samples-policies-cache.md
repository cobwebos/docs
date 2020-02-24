---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/13/2020
ms.author: dacoulte
ms.openlocfilehash: 31bf6382558565198f0f133c5df721b4a227cc5f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77370912"
---
|名称 |说明 |效果 |版本 |源 |
|---|---|---|---|
|[只应启用与 Redis 缓存的安全连接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |审核确认仅启用了通过 SSL 来与 Redis 缓存建立连接。 使用安全连接可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听攻击和会话劫持等网络层攻击 |Audit, Deny, 已禁用 |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json)
