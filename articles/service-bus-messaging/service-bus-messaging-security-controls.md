---
title: Azure 服务总线消息传送的安全控制
description: 用于评估 Azure 服务总线消息传送的安全控制清单
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903253"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Azure 服务总线消息传送的安全控制

本文介绍内置于 Azure 服务总线消息传送中的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务终结点支持| 是（仅限高级层） | 仅[服务总线高级层](service-bus-premium-messaging.md)支持 VNet 服务终结点。 |  |
| VNet 注入支持| 否 | |  |
| 网络隔离和防火墙支持| 是（仅限高级层） |  |  |
| 强制隧道支持| 否 |  |  |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | 通过[Azure Monitor 和警报](service-bus-metrics-azure-monitor.md)支持。 |  |
| 控制和管理平面日志记录和审核| 是 | 可用的操作日志。  | [服务总线诊断日志](service-bus-diagnostic-logs.md) |
| 数据平面日志记录和审核| 否 |  |

## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 身份验证| 是 | 通过[Azure Active Directory 托管服务标识](service-bus-managed-service-identity.md)管理。| [服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。 |
| 授权| 是 | 支持通过[RBAC](authenticate-application.md)和 SAS 令牌进行授权。 | [服务总线身份验证和授权](service-bus-authentication-and-authorization.md)。 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务器端加密： Microsoft 托管密钥 |  是，默认情况下，服务器端加密为静态。 |  |  |
| 服务器端加密：客户托管的密钥（BYOK） | 可以。 | 可以使用 Azure KeyVault 中的客户托管密钥来加密静态服务总线命名空间上的数据。 | [使用 Azure 门户配置客户托管的密钥，以便在静态上加密 Azure 服务总线数据](configure-customer-managed-key.md)  |
| 列级加密（Azure 数据服务）| N/A | |   |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 支持标准 HTTPS/TLS 机制。 |   |
| 加密的 API 调用| 是 | API 调用通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS 进行。 |   |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过[Azure 资源管理器 API](/rest/api/resources/)进行资源提供程序版本控制。|   |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。
