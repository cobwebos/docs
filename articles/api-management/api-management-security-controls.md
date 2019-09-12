---
title: Azure API 管理的安全控制
description: 用于评估 API 管理的安全控制清单
services: api-management
author: msmbaldwin
manager: rkarlin
ms.service: api-management
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e808f373ed3c977fb3263bc9e2e25bc602c7a7e1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886700"
---
# <a name="security-controls-for-api-management"></a>API 管理的安全控制

本文介绍了 API 管理中内置的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 | |
| VNet 注入支持| 是 | |
| 网络隔离和防火墙支持| 是 | 分别使用网络安全组 (NSG) 和 Azure 应用程序网关（或其他软件设备）。 |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |
| 控制和管理平面日志记录和审核| 是 | [Azure Monitor 活动日志](../azure-monitor/platform/activity-logs-overview.md) |
| 数据平面日志记录和审核| 是 | [Azure Monitor 诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)和 (可选) [Azure 应用程序见解](../azure-monitor/app/app-insights-overview.md)。  |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | |
| Authorization| 是 | |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 |
|---|---|--|
| 服务器端静态加密：Microsoft 托管的密钥 | 是 | 敏感数据（例如证书、密钥以及使用机密命名的值）使用服务托管的、基于服务实例的密钥进行加密。 |
| 服务器端加密：客户托管的密钥（BYOK） | 否 | 所有加密密钥都是基于服务实例的，也是通过服务托管的。 |
| 列级加密（Azure 数据服务）| 不可用 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | [快速路由](../expressroute/index.yml)和 VNet 加密由 [Azure 网络](../virtual-network/index.yml)提供。 |
| 加密的 API 调用| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)经 TLS 进行管理平面调用。 需要有效 JSON web 令牌 (JWT)。  可以通过 TLS 以及某个受支持的身份验证机制（例如，客户端证书或 JWT）对数据平面调用进行保护。
 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 使用 [Azure API 管理 DevOps 资源工具包](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>漏洞扫描误报

本部分介绍不影响 Azure API 管理的常见漏洞。

| 漏洞               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed 是在实现 TLS SessionTicket 扩展过程中出现的漏洞，在某些 F5 产品中发现。 该漏洞会导致从取消初始化的内存中泄露（“溢出”）最多 31 字节的数据。 这是由 TLS 堆栈填充通过客户端传递的会话 ID 导致的, 其数据用于使其长度为32位。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。