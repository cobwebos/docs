---
title: Azure API 管理的常见安全属性
description: 常见的安全属性评估 API 管理进行的检查表
services: api-management
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 040d628f9fe89f68a1c5ab91a1522c6a3cb724d9
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64508138"
---
# <a name="common-security-attributes-for-api-management"></a>API 管理的常见安全属性

安全性已集成到 Azure 服务的各个方面。 本文提供 API 管理中内置的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 （仅适用于服务端加密） | 使用加密敏感数据，例如证书、 密钥和机密命名的值是服务托管的每个服务的实例键。 |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | [快速路由](../expressroute/index.yml)，并由提供 VNet 加密[Azure 网络](../virtual-network/index.yml)。 |
| 加密密钥处理 （CMK，BYOK，等等。）| 否 | 所有加密密钥是每个服务实例，管理服务。 |
| 列级加密 （Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | 管理平面的调用是通过[Azure 资源管理器](../azure-resource-manager/index.yml)通过 TLS。 需要有效 JSON web 令牌 (JWT)。  可以使用 TLS 和一种支持的身份验证机制 （例如客户端证书或 JWT） 来保护数据平面调用。
 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 | |
| VNet 注入支持| 是 | |
| 网络隔离和防火墙支持| 是 | 使用网络安全组 (NSG) 和 Azure 应用程序网关 （或其他软件设备） 分别。 |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | |
| 授权| 是 | |


## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | [Azure Monitor 活动日志](../azure-monitor/platform/activity-logs-overview.md) |
| 数据平面日志记录和审核| 是 | [Azure Monitor 诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)和 （可选） [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 使用[Azure API 管理 DevOps 资源工具包](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>漏洞扫描的假正值

本部分介绍常见漏洞，不会影响 Azure API 管理。

| 漏洞               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016年-9244) | Ticketbleed 是某些 F5 产品中找到的 TLS SessionTicket 扩展插件实现中的漏洞。 它允许 （"出血"） 的最多 31 个字节的未初始化的内存中的数据泄露。 这由填充会话 ID、 客户端数据，使其 32 位与传递的 TLS 堆栈导致长时间。 |