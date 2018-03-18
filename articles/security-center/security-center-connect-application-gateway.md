---
title: "将 Microsoft Azure 应用程序网关连接到 Azure 安全中心 | Microsoft Docs"
description: "了解如何将应用程序网关与 Azure 安全中心集成，以增强资源的总体安全性。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>将 Microsoft Azure 应用程序网关连接到 Azure 安全中心
本文档可帮助你配置与应用程序网关 Web 应用程序防火墙 (WAF) 和安全中心的集成。

## <a name="why-connect-application-gateway"></a>为什么连接应用程序网关？
应用程序网关中的 WAF 可保护 Web 应用程序，使其免受常见的基于 Web 的攻击，例如 SQL 注入、跨站点脚本攻击和会话劫持。 安全中心与应用程序网关相集成，可防止和检测对环境中未受保护的 Web 应用程序的威胁。

## <a name="how-do-i-configure-this-integration"></a>如何配置此集成？
安全中心可在订阅中发现以前部署的 WAF 实例。 将这些解决方案连接到安全中心，以便允许集成警报。

> [!NOTE]
> 此外，还可以从安全中心的**建议**预配应用程序网关 WAF，如[添加 Web 应用程序防火墙](security-center-add-web-application-firewall.md)中所述。
>
>

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。

2. 在 **Microsoft Azure 菜单**上选择“安全中心”。 此时会打开“安全中心 - 概览”。

3. 在“概述”下，选择“安全解决方案”。

  ![安全中心概述](./media/security-center-connect-application-gateway/overview.png)

4. 在“已发现解决方案”下，找到 Microsoft 基于 SaaS 的 Web 应用程序防火墙并选择“连接”。

  ![已发现解决方案](./media/security-center-connect-application-gateway/connect.png)

5. 此时将打开“连接 WAF 解决方案”。  选择“连接”以将 WAF 与安全中心集成。

  ![连接 WAF 解决方案](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成应用程序网关 WAF。 若要详细了解安全中心，请参阅以下文章：

* [在安全中心集成安全解决方案](security-center-partner-integration.md)
* [将 Microsoft Advanced Threat Analytics 连接到安全中心](security-center-ata-integration.md)
* [将 Azure Active Directory Identity Protection 连接到安全中心](security-center-aadip-integration.md)
* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。
* [通过安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)。
* [Azure 安全中心常见问题解答](security-center-faq.md)。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/)。
