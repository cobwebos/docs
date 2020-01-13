---
title: Azure 安全中心内 Azure 安全产品的威胁检测警报
description: 本主题介绍 Azure 安全中心可在其中显示威胁警报的 Azure 安全产品
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913036"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Azure WAF 和 Azure DDoS 防护中的威胁检测警报

Azure 安全中心可以显示和收集以下 Azure 安全产品生成的威胁检测警报，（需要为每个产品提供单独的许可证）：

* [Azure WAF](#azure-waf)
* [Azure DDoS 防护](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 是基于开放式 Web 应用程序安全项目的核心规则集3.0 或2.2.9。 WAF 会自动更新以防止新的漏洞，无需其他配置。 WAF 警报会流式传输到安全中心。 有关 WAF 生成的警报的详细信息，请参阅[Web 应用程序防火墙 CRS 规则组和规则](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。

## Azure DDoS 保护<a name="azure-ddos"></a>

众所周知，分布式拒绝服务（DDoS）攻击非常易于执行。 它们已成为一个很好的安全问题，尤其是在将应用程序移到云时。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可以面向任何可通过 internet 访问的终结点。

Azure DDoS 保护与应用程序设计最佳做法相结合，提供对 DDoS 攻击的防御。 DDoS 保护提供不同的服务层。 有关详细信息，请参阅[Azure DDoS 保护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

有关 Azure DDoS 保护警报的列表，请参阅警报的[引用表](alerts-reference.md#alerts-azureddos)。