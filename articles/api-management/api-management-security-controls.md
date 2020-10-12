---
title: Azure API 管理的安全控制
description: 查看用于评估 API 管理的安全控件清单。 其中包括网络、标识和数据保护控件。
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: a147179f7b55e43379b3c3fa3a7a0767cc97b198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87902569"
---
# <a name="security-controls-for-api-management"></a>API 管理的安全控制

本文介绍了 API 管理中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 | 文档 |
|---|---|--|--|
| 服务终结点支持| 否 | |  |
| VNet 注入支持| 是 | |  |
| 网络隔离和防火墙支持| 是 | 分别使用网络安全组 (NSG) 和 Azure 应用程序网关（或其他软件设备）。 |  |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |  |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释| 文档 |
|---|---|--|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | | |
| 控制和管理平面日志记录和审核| 是 | [Azure Monitor 活动日志](../azure-monitor/platform/platform-logs-overview.md) | |
| 数据平面日志记录和审核| 是 | [Azure Monitor 诊断日志](../azure-monitor/platform/platform-logs-overview.md)和（可选）[Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  | |


## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释| 文档 |
|---|---|--|--|
| 身份验证| 是 | |  |
| 授权| 是 | |  |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 | 文档 |
|---|---|--|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 敏感数据（例如证书、密钥以及使用机密命名的值）使用服务托管的、基于服务实例的密钥进行加密。 |  |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 否 | 所有加密密钥均基于服务实例，并由服务管理。 |  |
| 列级加密（Azure 数据服务）| 空值 | |  |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | [快速路由](../expressroute/index.yml)和 VNet 加密由 [Azure 网络](../virtual-network/index.yml)提供。 |  |
| 加密的 API 调用| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)经 TLS 进行管理平面调用。 需要有效 JSON web 令牌 (JWT)。  可以使用 TLS 和受支持的身份验证机制之一（例如客户端证书或 JWT）保护数据平面调用。 |   |
 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释| 文档 |
|---|---|--|--|
| 配置管理支持（配置的版本控制等）| 是 | 使用 [Azure API 管理 DevOps 资源工具包](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>漏洞扫描误报

此部分记录不影响 Azure API 管理的常见漏洞。

| 漏洞               | 说明                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed 是在实现 TLS SessionTicket 扩展过程中出现的漏洞，在某些 F5 产品中发现。 该漏洞会导致从取消初始化的内存中泄露（“溢出”）最多 31 字节的数据。 这是由 TLS 堆栈导致的，该堆栈填充了会话 ID，从客户端传递，在带有数据的情况下有 32 位长。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[跨 Azure 服务的内置安全控制](../security/fundamentals/security-controls.md)。