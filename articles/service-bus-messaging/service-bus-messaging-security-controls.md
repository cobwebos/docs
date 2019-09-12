---
title: Azure 服务总线消息传送的安全控制
description: 用于评估 Azure 服务总线消息传送的安全控制清单
services: service-bus-messaging
ms.service: service-bus-messaging
author: axisc
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ac931ac7cf6c8609019b120388e1b6ca836f72b8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886728"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure 服务总线消息传送的安全控制

本文介绍内置于 Azure 服务总线消息传送中的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是（仅限高级层） | 仅[服务总线高级层](service-bus-premium-messaging.md)支持 VNet 服务终结点。 |
| VNet 注入支持| 否 | |
| 网络隔离和防火墙支持| 是（仅限高级层） |  |
| 强制隧道支持| 否 |  |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 通过 [Azure Monitor 和警报](service-bus-metrics-azure-monitor.md)支持。 |
| 控制和管理平面日志记录和审核| 是 | 操作日志可用；请参阅[服务总线诊断日志](service-bus-diagnostic-logs.md)。  |
| 数据平面日志记录和审核| 否 |  |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 通过 [Azure Active Directory 托管服务标识](service-bus-managed-service-identity.md)进行管理；请参阅[服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。|
| Authorization| 是 | 支持通过[RBAC](authenticate-application.md)和 SAS 令牌进行授权;请参阅[服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端静态加密：Microsoft 托管的密钥 |  默认情况下，服务器端静态加密为“是”。 | 目前尚不支持客户托管密钥和 BYOK。 客户端加密是客户端的责任 |
| 服务器端加密：客户托管的密钥（BYOK） | 否 |   |
| 列级加密（Azure 数据服务）| 不可用 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 支持标准的 HTTPS/TLS 机制。 |
| 加密的 API 调用| 是 | API 调用通过 [Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS 进行。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过 [Azure 资源管理器 API](/rest/api/resources/) 进行资源提供程序版本控制。|

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。