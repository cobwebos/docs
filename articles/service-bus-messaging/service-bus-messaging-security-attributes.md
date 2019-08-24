---
title: Azure 服务总线消息传送的安全属性
description: 用于评估 Azure 服务总线消息传送的安全属性清单
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 875754282f9e7a2b8dd92b0eb2d7e277ac842696
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013112"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Azure 服务总线消息传送的安全属性

本文记录了内置到 Azure 服务总线消息传送中的安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能）|  默认情况下，服务器端静态加密为“是”。 | 目前尚不支持客户托管密钥和 BYOK。 客户端加密是客户端的责任 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 支持标准的 HTTPS/TLS 机制。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |   |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | API 调用通过 [Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS 进行。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是（仅限高级层） | 仅[服务总线高级层](service-bus-premium-messaging.md)支持 VNet 服务终结点。 |
| VNet 注入支持| 否 | |
| 网络隔离和防火墙支持| 是（仅限高级层） |  |
| 强制隧道支持| 否 |  |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 通过 [Azure Monitor 和警报](service-bus-metrics-azure-monitor.md)支持。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 通过 [Azure Active Directory 托管服务标识](service-bus-managed-service-identity.md)进行管理；请参阅[服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。|
| Authorization| 是 | 支持通过[RBAC](authenticate-application.md)和 SAS 令牌进行授权;请参阅[服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。 |



## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 操作日志可用；请参阅[服务总线诊断日志](service-bus-diagnostic-logs.md)。  |
| 数据平面日志记录和审核| 否 |  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过 [Azure 资源管理器 API](/rest/api/resources/) 进行资源提供程序版本控制。|
