---
title: Azure 应用程序网关机器人保护概述中的 WAF
titleSuffix: Azure Web Application Firewall
description: 本文概述了应用程序网关机器人保护上的 web 应用程序防火墙（WAF）
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027090"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 应用程序网关机器人保护概述上的 Azure Web 应用程序防火墙

大约有20% 的 Internet 流量来自不良的机器人。 它们执行一些操作，例如抓取、扫描和查找 web 应用程序中的漏洞。 当在 Web 应用程序防火墙（WAF）上停止这些 bot 时，他们将无法攻击你。 还不能使用资源和服务，例如后端和其他底层基础结构。

可以为 WAF 启用托管机器人保护规则集，以便阻止或记录来自已知恶意 IP 地址的请求。 IP 地址源自 Microsoft 威胁智能源。 Intelligent Security Graph 为 Microsoft 威胁智能助力，它已得到 Azure Security Center 等多项服务的运用。

> [!IMPORTANT]
> 机器人保护规则集当前以公共预览版提供，并随预览版服务级别协议一起提供。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 。

## <a name="use-with-owasp-rulesets"></a>使用 with OWASP 规则集

可以将机器人保护规则集与任何 OWASP 规则集配合使用（2.2.9、3.0 和3.1）。 在任意给定时间，只能使用一个 OWASP 规则集。 机器人保护规则集包含另一个规则，该规则显示在其自己的规则集中。 标题**Microsoft_BotManagerRuleSet_0 .1**，你可以像其他 OWASP 规则一样启用或禁用它。

![机器人规则集](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>规则集更新

每日从 Microsoft 威胁情报源更新一次已知的错误 IP 地址的机器人缓解规则集列表，使其与 bot 保持同步。 即使 bot 攻击向量发生变化，你的 web 应用程序也会持续受到保护。

## <a name="next-steps"></a>后续步骤

- [为 Azure 应用程序网关（预览版）上的 Web 应用程序防火墙配置机器人保护](bot-protection.md)
