---
title: 常见 Azure 服务总线消息传送的安全属性
description: 评估 Azure 服务总线消息传送的通用安全属性进行的检查表
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513519"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>常见 Azure 服务总线消息传送的安全属性

安全性已集成到 Azure 服务的各个方面。 本文介绍 Azure 服务总线消息传送中内置的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>|  默认情况下，是用于服务器端静态加密。 | 尚不支持客户托管的密钥和 BYOK。 客户端加密是客户端的责任 |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支持标准的 HTTPS/TLS 机制。 |
| 加密密钥处理 （CMK，BYOK，等等。）| 否 |   |
| 列级加密 （Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | API 调用都是通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 （仅适用于高级层） | 支持 VNet 服务终结点[服务总线高级层](service-bus-premium-messaging.md)仅。 |
| VNet 注入支持| 否 | |
| 网络隔离和防火墙支持| 是 （仅适用于高级层） |  |
| 强制隧道支持| 否 |  |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 支持通过[Azure Monitor 和警报](service-bus-metrics-azure-monitor.md)。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 通过管理[Azure Active Directory 托管服务标识](service-bus-managed-service-identity.md); 请参阅[服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。|
| 授权| 是 | 支持通过授权[RBAC](service-bus-role-based-access-control.md) （预览版） 和 SAS 令牌; 请参阅[服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。 |



## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 操作日志才会有效。请参阅[服务总线诊断日志](service-bus-diagnostic-logs.md)。  |
| 数据平面日志记录和审核| 否 |  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 支持通过资源提供程序版本控制[Azure 资源管理器 API](/rest/api/resources/)。|
