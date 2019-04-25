---
title: Azure Front Door 服务 - 应用程序层安全性 | Microsoft Docs
description: 本文介绍如何通过 Azure Front Door 服务来保护应用程序后端，确保其安全性
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193911"
---
# <a name="application-layer-security-with-front-door"></a>通过 Front Door 实现应用程序层安全性
Azure Front Door 服务提供 Web 应用程序保护功能，可以确保 Web 应用程序不受网络攻击和常见的 Web 漏洞攻击，例如 SQL 注入或跨站点脚本 (XSS)。 为 HTTP(S) 前端启用的 Front Door 的应用程序层安全性分布在全球并始终处于启用状态，可以阻止攻击者在 Azure 的网络边缘发动恶意攻击，使之远离你的后端。 Front Door 在安全性和性能优化方面进行了强化，可以为最终用户提供快速且安全的 Web 体验。

## <a name="application-protection"></a>应用程序保护
Front Door 的应用程序保护在全球的每个边缘环境中根据应用程序的情况进行配置，可以自动阻止非 HTTP(S) 流量到达 Web 应用程序。 我们的多租户分布式体系结构可以在不牺牲性能的情况下实现大规模的全球保护。 对于 HTTP(S) 工作负荷，Front Door 的 Web 应用程序保护服务提供了适用于自定义规则的富规则引擎、针对常见攻击的预配置规则集，以及适用于所有与规则相符的请求的详细日志记录。 支持包括“允许”、“阻止”或“仅记录”在内的灵活操作。

## <a name="custom-access-control-rules"></a>自定义访问控制规则
- **IP 允许列表和阻止列表：** 可以配置自定义规则以控制对 web 应用程序基于客户端 IP 地址的列表的访问。 支持 IPv4 和 IPv6
- **地理基于的访问控制：** 可以配置自定义规则以控制对基于国家/地区代码的客户端 IP 是从 web 应用程序的访问
- **HTTP 筛选参数：** 您可以配置基于 http （s） 请求参数包括标头、 URL 和查询字符串相匹配的自定义访问规则

## <a name="azure-managed-rules"></a>Azure 托管的规则
- 默认情况下，会启用预配置的一组规则来防范最常见的 OWASP 漏洞。 预览版的规则组包括 sqli 和 xss 请求检查。 将会添加其他规则。 可以选择从“仅记录”操作着手，验证应用程序的预配置规则是否可以正常使用 

## <a name="rate-limiting"></a>速率限制
- 速率控制规则用于限制任何客户端 IP 发出的异常高的流量。  对于客户端 IP 在一分钟的期限内允许的 Web 请求数，可以设置一个阈值。

## <a name="centralized-protection-policy"></a>集中保护策略
- 可以定义多个保护规则，将它们按优先顺序添加到策略。 在允许异常方面，自定义规则比托管规则集的优先级要高。 单项规则已关联到你的 Web 应用程序。  同一 Web 应用程序保护策略会复制到所有位置的所有边缘服务器，确保所有区域的安全策略一致

## <a name="configuration"></a>配置
- 在预览版发布期间，可以使用 REST API、PowerShell 或 CLI 来创建并部署 Front Door 的应用程序保护规则和策略。 在服务公开发布之前，会支持门户访问。 


## <a name="monitoring"></a>监视
Front Door 提供通过实时指标来监视 Web 应用程序的功能，可以防范攻击。这些指标集成到 Azure Monitor 中，可以跟踪警报并轻松地监视趋势。

## <a name="pricing"></a>定价
Front Door 的应用层安全性在预览版中免费。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
