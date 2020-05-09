---
title: 应用程序网关上的 Azure Web 应用程序防火墙-常见问题
description: 本文提供了有关应用程序网关上 Web 应用程序防火墙的常见问题的解答
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 57081cd948bcee1261415eae31f91b3c61f08c9f
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842846"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>应用程序网关上的 Azure Web 应用程序防火墙常见问题解答

本文解答了有关应用程序网关特性和功能的 Azure Web 应用程序防火墙（WAF）的常见问题。 

## <a name="what-is-azure-waf"></a>什么是 Azure WAF？

Azure WAF 是一个 web 应用程序防火墙，可帮助保护 web 应用程序免受常见的威胁，例如 SQL 注入、跨站点脚本和其他 web 攻击。 你可以定义一个包含自定义和托管规则组合的 WAF 策略，以控制对你的 web 应用程序的访问。

Azure WAF 策略可应用于应用程序网关或 Azure 前门上托管的 web 应用程序。

## <a name="what-features-does-the-waf-sku-support"></a>WAF SKU 支持哪些功能？

WAF SKU 支持标准 SKU 中提供的所有功能。

## <a name="how-do-i-monitor-waf"></a>如何监视 WAF？

通过诊断日志记录监视 WAF。 有关详细信息，请参阅[应用程序网关的诊断日志记录和指标](../../application-gateway/application-gateway-diagnostics.md)。

## <a name="does-detection-mode-block-traffic"></a>检测模式是否会阻止流量？

不是。 检测模式仅记录触发 WAF 规则的流量。

## <a name="can-i-customize-waf-rules"></a>我可以自定义 WAF 规则吗？

可以。 有关详细信息，请参阅[自定义 WAF 规则组和规则](application-gateway-customize-waf-rules-portal.md)。

## <a name="what-rules-are-currently-available-for-waf"></a>WAF 目前支持哪些规则？

WAF 目前支持 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229)、[3.0](application-gateway-crs-rulegroups-rules.md#owasp30) 和 [3.1](application-gateway-crs-rulegroups-rules.md#owasp31)。 这些规则针对开放 Web 应用程序安全项目 (OWASP) 识别到的 10 大漏洞中的大多数漏洞提供基准安全性。 

* SQL 注入保护
* 跨站点脚本防护
* 防范常见 Web 攻击，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击
* 防止 HTTP 协议违反行为
* 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头
* 防止自动程序、爬网程序和扫描程序
* 检测常见应用程序错误配置（即 Apache、IIS 等）

有关详细信息，请参阅 [OWASP 10 大漏洞](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)。

## <a name="does-waf-support-ddos-protection"></a>WAF 是否支持 DDoS 防护？

可以。 可以在部署了应用程序网关的虚拟网络中启用 DDoS 保护。 此设置确保 Azure DDoS 防护服务同时保护应用程序网关虚拟 IP (VIP)。


## <a name="next-steps"></a>后续步骤

- 了解[Azure Web 应用程序防火墙](../overview.md)。
- 详细了解[Azure 前门](../../frontdoor/front-door-overview.md)。
