---
title: Azure API 管理的安全控制
description: 用于评估 API 管理的安全控制清单
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751139"
---
# <a name="security-controls-for-api-management"></a>API 管理的安全控制

本文介绍了 API 管理中内置的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务终结点支持| 否 | |  |
| VNet 注入支持| 是 | |  |
| 网络隔离和防火墙支持| 是 | 分别使用网络安全组（NSG）和 Azure 应用程序网关（或其他软件设备）。 |  |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |  |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | | |
| 控制和管理平面日志记录和审核| 是 | [Azure Monitor 活动日志](../azure-monitor/platform/platform-logs-overview.md) | |
| 数据平面日志记录和审核| 是 | [Azure Monitor 诊断日志](../azure-monitor/platform/platform-logs-overview.md)和（可选） [Azure 应用程序见解](../azure-monitor/app/app-insights-overview.md)。  | |


## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 身份验证| 是 | |  |
| 授权| 是 | |  |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 | 文档 |
|---|---|--|--|
| 服务器端加密： Microsoft 托管密钥 | 是 | 敏感数据（如证书、密钥和机密命名值）将用服务托管的每个服务实例密钥进行加密。 |  |
| 服务器端加密：客户托管的密钥（BYOK） | 否 | 所有加密密钥均为每个服务实例，并由服务管理。 |  |
| 列级加密（Azure 数据服务）| N/A | |  |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | [Express Route](../expressroute/index.yml)和 VNet 加密由[Azure 网络](../virtual-network/index.yml)提供。 |  |
| 加密的 API 调用| 是 | 管理平面调用通过 TLS 通过[Azure 资源管理器](../azure-resource-manager/index.yml)进行。 需要有效 JSON web 令牌 (JWT)。  数据平面调用可以通过 TLS 和支持的身份验证机制之一（例如，客户端证书或 JWT）来保护。 |   |
 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明| 文档 |
|---|---|--|--|
| 配置管理支持（配置的版本控制等）| 是 | 使用[AZURE API 管理 DevOps 资源工具包](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>漏洞扫描误报

本部分介绍不影响 Azure API 管理的常见漏洞。

| 漏洞               | Description                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed （CVE-2016-9244） | Ticketbleed 是在某些 F5 产品中发现的 TLS SessionTicket 扩展实现中的漏洞。 它允许未初始化的内存中最多包含31个字节的数据。 这是由 TLS 堆栈填充通过客户端传递的会话 ID 导致的，其数据用于使其长度为32位。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。