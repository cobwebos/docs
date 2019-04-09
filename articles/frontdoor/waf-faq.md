---
title: Azure web 应用程序防火墙-常见问题
description: 此页提供有关 Azure 第一道防线服务常见问题的解答
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255914"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure web 应用程序防火墙的常见问题解答

本文回答有关 Azure web 应用程序防火墙 (WAF) 功能和功能的常见问题。 

## <a name="what-is-azure-waf"></a>什么是 Azure WAF？

Azure WAF，有助于防止 SQL 注入、 跨站点脚本和其他 web 攻击的常见威胁保护 web 应用程序的 web 应用程序防火墙。 可以定义自定义和托管的规则来控制对 web 应用程序的组合组成的 WAF 策略。

Azure WAF 策略可以应用于应用程序网关或 Azure 第一道防线服务上托管的 web 应用程序。

## <a name="what-is-waf-for-azure-front-door-service"></a>什么是 WAF for Azure 第一道防线服务？ 

Azure 的第一道防线是一个高度可缩放的全球分布式应用程序和内容交付网络。 Azure WAF，与第一道防线，集成时停止拒绝服务和目标应用程序在 Azure 网络边缘的攻击，攻击源进入虚拟网络之前, 提供保护，而无需牺牲性能。

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>如何将收取 Azure WAF 的第一道防线？
公共预览期间，WAF 使用情况，第一道防线是免费。 请注意，额外第一道防线费用。 请参阅第一道防线服务定价[此处](https://azure.microsoft.com/pricing/details/frontdoor/)。

## <a name="does-azure-waf-support-https"></a>Azure WAF 支持 HTTPS？

第一道防线服务提供 SSL 卸载。 WAF 与第一道防线本机集成，并解密后，可以检查请求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支持 IPv6？

是的。 你可以配置为 IPv4 和 IPv6 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>托管规则集的更新？

我们尽力保持与不断变化的威胁环境。 一旦更新新的规则，则将它添加到默认规则集与新的版本号。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>什么是传播时，如果我对我 WAF 策略进行更改？

部署 WAF 策略全局通常需要大约 5 分钟，通常更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF 策略是否可以为不同的区域不同？

与第一道防线服务集成时，WAF 是一个全局资源。 在所有的第一道防线位置适用相同的配置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何为我后端仅从第一道防线是限制访问？

在后端以仅第一道防线出站 IP 地址范围允许和拒绝来自 Internet 的任何直接访问，可以配置 IP 访问控制列表。 服务标记，以便在虚拟网络上使用受支持。 此外，你可以验证 X 转发主机 HTTP 标头字段为 web 应用程序有效。




## <a name="which-azure-waf-options-should-i-choose"></a>应选择哪些 Azure WAF 选项？

应用在 Azure 中的 WAF 策略时，有两个选项。 WAF 与 Azure 第一道防线是一个全局分布式的边缘安全解决方案。 WAF 应用程序网关是一个区域、 专用解决方案。 我们建议你选择基于你的整体性能和安全性要求的解决方案。 有关详细信息，请参阅[负载平衡与 Azure 的应用程序交付套件](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>在所有的集成平台是否支持相同的 WAF 功能？

目前，ModSec CRS 2.2.9 和 CRS 3.0 规则仅支持在应用程序网关 waf。 速率限制、 地区筛选和 Azure 的托管默认规则集规则仅支持 WAF 在 Azure 第一道防线。

## <a name="is-ddos-protection-integrated-with-front-door"></a>与第一道防线集成 DDoS 防护？ 

全球分布在 Azure 网络边缘，Azure 第一道防线可以 absorb 和地理隔离较大的卷的攻击。 您可以创建自定义 WAF 策略应用到自动块和速率限制 http （s） 攻击具有已知签名。 更多多，您可以启用标准 DDoS 保护在其中部署你的后端的 VNet。 Azure DDoS 保护标准的客户可享受其他权益，包括在受攻击的成本保护、 SLA 保证和的访问权限向专家 DDoS 快速响应团队即时帮助。 

## <a name="next-steps"></a>后续步骤

- 了解如何[Azure web 应用程序防火墙](waf-overview.md)。
- 详细了解如何[Azure 第一道防线](front-door-overview.md)。
