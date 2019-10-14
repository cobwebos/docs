---
title: Azure web 应用程序防火墙-常见问题
description: 本页提供有关 Azure 前门服务的常见问题的解答
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f6e0ea7ca5b9b131a8d7d7c2c6546130a7d020b3
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302843"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure web 应用程序防火墙常见问题解答

本文解答了有关 Azure web 应用程序防火墙（WAF）特性和功能的常见问题。 

## <a name="what-is-azure-waf"></a>什么是 Azure WAF？

Azure WAF 是一个 web 应用程序防火墙，可帮助保护 web 应用程序免受常见的威胁，例如 SQL 注入、跨站点脚本和其他 web 攻击。 你可以定义一个包含自定义和托管规则组合的 WAF 策略，以控制对你的 web 应用程序的访问。

Azure WAF 策略可应用于应用程序网关或 Azure 前门服务上托管的 web 应用程序。

## <a name="what-is-waf-for-azure-front-door-service"></a>Azure 前门服务的 WAF 是什么？ 

Azure 前门是高度可缩放的全球分布式应用程序和内容交付网络。 将 Azure WAF 与 Azure 前门集成后，会在 Azure 网络边缘停止拒绝服务和目标应用攻击。 此保护会在攻击源有机会进入 vitrual 网络之前发生。 WAF for Azure 前门服务提供保护，而不影响性能。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支持 HTTPS？

前门服务提供 SSL 卸载。 WAF 本机与前门集成，并可以在解密后检查请求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支持 IPv6？

是。 可为 IPv4 和 IPv6 配置 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>托管规则集的最新版本是什么？

我们尽最大努力维持不断变化的威胁。 更新新规则后，将使用新的版本号将其添加到默认规则集。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果更改 WAF 策略，会发生什么传播时间？

全局部署 WAF 策略通常花费大约5分钟的时间，并且通常会更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>不同区域的 WAF 策略是否可以不同？

与前门服务集成后，WAF 是全局资源。 同一配置适用于所有前门位置。
 

## <a name="which-azure-waf-options-should-i-choose"></a>我应该选择哪些 Azure WAF 选项？

在 Azure 中应用 WAF 策略时，有两个选项可供选择。 使用 Azure 前门的 WAF 是一种全球分布的边缘安全解决方案。 使用应用程序网关的 WAF 是一个区域专用解决方案。 建议选择一个基于整体性能和安全要求的解决方案。 有关详细信息，请参阅[通过 Azure 的应用程序交付套件进行负载平衡](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>是否支持所有集成平台中的相同 WAF 功能？

目前，仅在应用程序网关上 WAF 支持 ModSec CRS 2.2.9 和 CRS 3.0 规则。 只有 Azure 前门上的 WAF 支持速率限制、异地筛选和 Azure 托管默认规则集规则。

## <a name="is-ddos-protection-integrated-with-front-door"></a>是否与前门集成了 DDoS 防护？ 

在 Azure 网络边缘全球分布，Azure 前门可以吸收并在地理上隔离大容量攻击。 你可以创建自定义 WAF 策略，以自动阻止和速率限制具有已知签名的 http （s）攻击。 此外，还可以在部署后端的 VNet 中启用 DDoS 保护标准。 Azure DDoS 保护标准客户可获得更多好处，包括成本保护、SLA 保证，以及从 DDoS 快速响应团队访问专家，以在攻击期间立即获得帮助。 

建议在生产环境中锁定后端，以减少 DDoS 攻击面。 请参阅[如何实现锁定对我的后端的访问仅限于 Azure 前门？](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>后续步骤

- 了解 [Azure Web 应用程序防火墙](waf-overview.md)。
- 详细了解[Azure 前门](front-door-overview.md)。
