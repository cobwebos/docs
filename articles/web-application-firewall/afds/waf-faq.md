---
title: Azure 前门服务上的 azure Web 应用程序防火墙-常见问题
description: 本文提供有关 Azure 前门上的 Web 应用程序防火墙的常见问题的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: d129c37c909c630623f8a41c06da9aa80e4e2392
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837628"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure 前门服务上的 Azure Web 应用程序防火墙常见问题解答

本文解答有关 azure 前门服务特性和功能上的 Azure Web 应用程序防火墙（WAF）的常见问题。 

## <a name="what-is-azure-waf"></a>什么是 Azure WAF？

Azure WAF 是一个 web 应用程序防火墙，可帮助保护 web 应用程序免受常见的威胁，例如 SQL 注入、跨站点脚本和其他 web 攻击。 你可以定义一个包含自定义和托管规则组合的 WAF 策略，以控制对你的 web 应用程序的访问。

Azure WAF 策略可应用于应用程序网关或 Azure 前门上托管的 web 应用程序。

## <a name="what-is-waf-on-azure-front-door"></a>什么是 Azure 前门上的 WAF？ 

Azure 前门是高度可缩放的全球分布式应用程序和内容交付网络。 Azure WAF 与前门集成后，可在 Azure 网络边缘停止拒绝服务和目标应用程序攻击，在进入虚拟网络之前，接近攻击源，提供保护，而不影响性能。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支持 HTTPS？

前门提供 TLS 卸载。 WAF 本机与前门集成，并可以在请求解密后检查请求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支持 IPv6？

可以。 可为 IPv4 和 IPv6 配置 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>托管规则集的最新版本是什么？

我们尽最大努力维持不断变化的威胁。 更新新规则后，将使用新的版本号将其添加到默认规则集。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果更改 WAF 策略，会发生什么传播时间？

全局部署 WAF 策略通常花费大约5分钟的时间，并且通常会更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>不同区域的 WAF 策略是否可以不同？

与前门集成后，WAF 是全局资源。 同一配置适用于所有前门位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何实现仅限前门访问我的后端：

你可以在后端配置 IP 访问控制列表，使其仅允许前门出站 IP 地址范围，并拒绝来自 Internet 的任何直接访问。 支持在虚拟网络上使用服务标记。 此外，还可以验证 "X 转发的主机 HTTP 标头" 字段对你的 web 应用程序是否有效。

## <a name="which-azure-waf-options-should-i-choose"></a>我应该选择哪些 Azure WAF 选项？

在 Azure 中应用 WAF 策略时，有两个选项可供选择。 使用 Azure 前门的 WAF 是一种全球分布的边缘安全解决方案。 使用应用程序网关的 WAF 是一个区域专用解决方案。 建议选择一个基于整体性能和安全要求的解决方案。 有关详细信息，请参阅[通过 Azure 的应用程序交付套件进行负载平衡](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>是否支持所有集成平台中的相同 WAF 功能？

目前，应用程序网关上的 WAF 只支持 ModSec CRS 2.2.9、CRS 3.0 和 CRS 3.1 规则。 只有 Azure 前门上的 WAF 支持速率限制、异地筛选和 Azure 托管默认规则集规则。

## <a name="is-ddos-protection-integrated-with-front-door"></a>是否与前门集成了 DDoS 防护？ 

在 Azure 网络边缘全球分布，Azure 前门可以吸收并在地理上隔离大容量攻击。 你可以创建自定义 WAF 策略，以自动阻止和速率限制具有已知签名的 http （s）攻击。 此外，还可以在部署后端的 VNet 中启用 DDoS 保护标准。 Azure DDoS 保护标准客户可获得更多好处，包括成本保护、SLA 保证，以及从 DDoS 快速响应团队访问专家，以在攻击期间立即获得帮助。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>为什么将超出阈值的其他请求传递到我的后端服务器？

速率限制规则可以限制来自任何客户端 IP 地址的异常高流量。 可以在一分钟或五分钟的持续时间内，配置从客户端 IP 地址允许的 web 请求数的阈值。 对于粒度速率控制，可以将速率限制与其他匹配条件（如 HTTP （S）参数匹配）组合在一起。 

来自同一客户端的请求通常会到达同一前门服务器。 在这种情况下，会看到超出阈值的其他请求会立即被阻止。 

但是，来自同一客户端的请求可能会到达尚未刷新速率限制计数器的另一个前端服务器。 例如，客户端可能会为每个请求打开一个新连接，并且阈值较低。 在这种情况下，对新前门服务器的第一个请求将通过速率限制检查。 速率限制阈值通常设置为 "高" 以防止来自任何客户端 IP 地址的拒绝服务攻击。 对于极低的阈值，可能会看到超出阈值的其他请求通过。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Web 应用程序防火墙](../overview.md)。
- 详细了解[Azure 前门](../../frontdoor/front-door-overview.md)。
