---
title: Azure Web 应用程序防火墙 - 常见问题
description: 本文提供了有关 Azure 前门 Web 应用程序防火墙的常见问题的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: fefbb038cec0c061f1fd191a77164880372555a0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314324"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure 前门服务上 Azure Web 应用程序防火墙的常见问题

本文回答了有关 Azure Web 应用程序防火墙 （WAF） 特性和功能的常见问题。 

## <a name="what-is-azure-waf"></a>什么是 Azure WAF？

Azure WAF 是一种 Web 应用程序防火墙，可帮助保护 Web 应用程序免受常见威胁（如 SQL 注入、跨站点脚本和其他 Web 漏洞利用）。 您可以定义由自定义规则和托管规则组合组成的 WAF 策略，以控制对 Web 应用程序的访问。

Azure WAF 策略可以应用于应用程序网关或 Azure 前门上托管的 Web 应用程序。

## <a name="what-is-waf-on-azure-front-door"></a>Azure 前门上的 WAF 是什么？ 

Azure 前门是一个高度可扩展的全球分布式应用程序和内容交付网络。 Azure WAF 与前门集成后，将停止 Azure 网络边缘的拒绝服务和目标应用程序攻击，这些攻击在攻击源进入虚拟网络之前靠近攻击源，从而在不牺牲性能的情况下提供保护。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支持 HTTPS？

前门提供 TLS 卸载。 WAF 与前门本机集成，可在解密后检查请求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支持 IPv6？

是的。 您可以为 IPv4 和 IPv6 配置 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>托管规则集的最新情况如何？

我们尽最大努力跟上不断变化的威胁形势。 更新新规则后，该规则将添加到具有新版本号的默认规则集中。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果更改 WAF 策略，传播时间是多少？

在全球部署 WAF 策略通常需要大约 5 分钟，并且通常完成得更快。

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF 策略对于不同区域是否可以不同？

与前门集成时，WAF 是一种全局资源。 相同的配置适用于所有前门位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何限制仅从前门进入后端？

您可以在后端配置 IP 访问控制列表，以仅允许前门出站 IP 地址范围，并拒绝从 Internet 直接访问。 支持服务标记，供您在虚拟网络上使用。 此外，还可以验证 X-前行主机 HTTP 标头字段是否对 Web 应用程序有效。

## <a name="which-azure-waf-options-should-i-choose"></a>我应该选择哪些 Azure WAF 选项？

在 Azure 中应用 WAF 策略时有两个选项。 带 Azure 前门的 WAF 是一个全局分布的边缘安全解决方案。 带应用程序网关的 WAF 是一种区域性的专用解决方案。 我们建议您根据整体性能和安全要求选择解决方案。 有关详细信息，请参阅使用[Azure 的应用程序交付套件进行负载平衡](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>您是否在所有集成平台上支持相同的 WAF 功能？

目前，ModSec CRS 2.2.9、CRS 3.0 和 CRS 3.1 规则仅支持应用程序网关上的 WAF。 速率限制、地理筛选和 Azure 托管默认规则集规则仅支持 Azure 前门的 WAF。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 防护是否与前门集成？ 

Azure 前门在 Azure 网络边缘全局分布，可以吸收大容量攻击并将其在地理位置上隔离。 您可以创建自定义 WAF 策略，以自动阻止和速率限制 http（s） 具有已知签名的攻击。 此外，您还可以在部署后端的 VNet 上启用 DDoS 保护标准。 Azure DDoS 保护标准客户将获得其他好处，包括成本保护、SLA 保证以及 DDoS 快速反应团队的专家访问，以便在攻击期间立即获得帮助。

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>为什么超出为速率限制规则配置的阈值的其他请求传递到后端服务器？

速率限制规则可以限制来自任何客户端 IP 地址的异常高流量。 您可以在一分钟或五分钟的持续时间内，针对客户端 IP 地址允许的 Web 请求数配置阈值。 对于粒度速率控制，速率限制可以与其他匹配条件（如 HTTP（S） 参数匹配相结合。 

来自同一客户端的请求通常到达同一个前门服务器。 在这种情况下，您将看到高于阈值的其他请求立即被阻止。 

但是，来自同一客户端的请求可能会到达尚未刷新速率限制计数器的不同前门服务器。 例如，客户端可能为每个请求打开一个新连接，并且阈值较低。 在这种情况下，对新的前门服务器的第一个请求将通过速率限制检查。 速率限制阈值通常设置高，以防御来自任何客户端 IP 地址的拒绝服务攻击。 对于非常低的阈值，您可能会看到高于阈值的其他请求通过。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Web 应用程序防火墙](../overview.md)。
- 了解有关[Azure 前门 的更多详细信息](../../frontdoor/front-door-overview.md)。
