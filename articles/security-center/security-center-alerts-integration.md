---
title: Azure 安全中心中的 Azure 安全产品集成 |Microsoft Docs
description: 本主题介绍与 Azure 安全中心集成的 Azure 安全产品。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 6560db7e2f1cb363e0b8ca7af3a08f6babd9b36b
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202412"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Azure 安全中心中的 Azure 安全产品集成

Azure 安全中心提供了其他 Microsoft 许可证来使用以下安全产品：

* [Azure WAF](#azure-waf)
* [Azure DDoS 防护](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序正逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 是基于开放式 Web 应用程序安全项目的核心规则集3.0 或2.2.9。 WAF 会自动更新以防止新的漏洞, 无需其他配置。 WAF 警报会流式传输到安全中心。 有关 WAF 生成的警报的详细信息，请参阅[Web 应用程序防火墙 CRS 规则组和规则](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)。

## Azure DDoS 保护<a name="azure-ddos"></a>

众所周知, 分布式拒绝服务 (DDoS) 攻击非常易于执行。 它们已成为一个很好的安全问题，尤其是在将应用程序移到云时。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可以面向任何可通过 internet 访问的终结点。

Azure DDoS 保护与应用程序设计最佳做法相结合，提供对 DDoS 攻击的防御。 DDoS 保护提供不同的服务层。 有关详细信息, 请参阅[Azure DDoS 保护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

DDoS 保护标准可缓解以下攻击类型：

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**检测到容量耗尽攻击**|这种攻击的目标是通过大量看似合法的通信淹没网络层。 它包括 UDP 洪水、放大洪水以及其他欺骗性数据包洪水。 "DDoS 保护" 标准通过使用全局网络缩放自动吸收和清理这些潜在的多字节攻击，从而缓解这些攻击。|
|**检测到协议攻击**|这些攻击通过利用第3层和第4层协议堆栈中的漏洞，使目标无法访问。 它包括 SYN 洪水攻击、反射攻击和其他协议攻击。 DDoS 保护标准通过与客户端交互来区分恶意流量和合法流量并阻止恶意流量，从而缓解这些攻击。|
|**检测到资源 (应用程序) 层攻击**|这些攻击以 web 应用程序数据包为目标来中断主机之间的数据传输。 这些攻击包括 HTTP 协议冲突、SQL 注入、跨站点脚本和其他第 7 层攻击。 使用带有 DDoS 保护标准的 Azure 应用程序网关 WAF 来防范这些攻击。 Azure Marketplace 还提供了第三方 WAF 产品/服务。|
