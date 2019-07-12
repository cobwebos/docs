---
title: 安全中心与 Azure 安全产品的集成 |Microsoft Docs
description: 本主题提供了已与 Azure 安全中心集成的 Azure 安全产品。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571733"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>与 ASC 的 Azure 安全产品的安全中心集成

安全中心为载入其他 Microsoft 许可证的客户提供其结果记录到安全中心，并以统一的方式查看它们。

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure 应用程序网关提供的 Web 应用程序防火墙 (WAF) 可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞伤害。

Web 应用程序正逐渐成为利用常见已知漏洞的恶意攻击的目标。 应用程序网关 WAF 基于在核心规则集 (CRS) 3.0 或 2.2.9 中打开 Web Application Security Project (OWASP)。 WAF 会自动更新，以避免出现新漏洞，使用无需额外配置。 生成的 WAF 警报流式传输到安全中心。 有关生成警报的 WAF 的详细信息，请参阅此[一文](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911)。

## Azure DDoS <a name="azure-ddos"></a>

分布式的拒绝服务 (DDoS) 攻击是已知能够轻松地执行。 因此，它们已变得很好的安全隐患的客户而言，要移动到云应用程序。 

DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可以面向任何终结点，可通过 Internet 访问。

Azure DDoS 保护，结合应用程序设计最佳做法，提供对 DDoS 攻击的防御。 Azure DDoS 保护提供了不同的服务层。 有关详细信息，请参阅[Azure DDoS 保护概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

DDoS 保护标准可缓解以下攻击类型：

> [!div class="mx-tableFixed"]

|警报|描述|
|---|---|
|**检测到的容量耗尽攻击**|此攻击的目标是流量的涌入网络层借助大量看似合法。 它包括 UDP 洪水、放大洪水以及其他欺骗性数据包洪水。 借助 Azure 的全球网络规模，DDoS 保护标准通过自动吸收和清理这些潜在的数千兆字节攻击，从而缓解这些攻击。|
|**检测到的协议攻击**|这些攻击通过利用第 3 层中的漏洞和层 4 协议堆栈使目标无法访问。 它包括 SYN 洪水攻击、反射攻击和其他协议攻击。 DDoS 保护标准通过与客户端交互来区分恶意流量和合法流量并阻止恶意流量，从而缓解这些攻击。|
|**检测到的资源 （应用程序） 层攻击**|这些攻击以 web 应用程序数据包来中断主机之间的数据传输为目标。 这些攻击包括 HTTP 协议冲突、SQL 注入、跨站点脚本和其他第 7 层攻击。 使用 Azure 应用程序网关 web 应用程序防火墙，DDoS 保护标准，以防范这些攻击。 还有第三方 web 应用程序防火墙产品/服务可在 Azure Marketplace 中。|
