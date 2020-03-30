---
title: Azure 应用程序网关自动程序保护概述上的 WAF
titleSuffix: Azure Web Application Firewall
description: 本文概述了应用程序网关机器人保护方面的 Web 应用程序防火墙 （WAF）
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027090"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure 应用程序防火墙上的 Azure 应用程序网关自动程序保护概述

大约20%的互联网流量来自不良机器人。 他们执行刮擦、扫描和查找 Web 应用程序中的漏洞等操作。 当这些自动程序在 Web 应用程序防火墙 （WAF） 停止时，它们不能攻击您。 它们也不能占用您的资源和服务，例如后端和其他底层基础结构。

您可以为 WAF 启用托管自动程序保护规则集，以阻止或记录来自已知恶意 IP 地址的请求。 IP 地址源自 Microsoft 威胁智能源。 Intelligent Security Graph 为 Microsoft 威胁智能助力，它已得到 Azure Security Center 等多项服务的运用。

> [!IMPORTANT]
> 自动程序保护规则集当前处于公共预览版中，并且提供预览服务级别协议。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 Microsoft Azure  [预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="use-with-owasp-rulesets"></a>与 OWASP 规则集一起使用

您可以将 Bot 保护规则集与任何 OWASP 规则集（2.2.9、3.0 和 3.1）一起使用。 在任何给定时间只能使用一个 OWASP 规则集。 自动程序保护规则集包含一个出现在其自己的规则集中的其他规则。 它的标题是**Microsoft_BotManagerRuleSet_0.1，** 你可以像其他OWASP规则一样启用或禁用它。

![机器人规则集](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>规则集更新

已知不良 IP 地址的自动程序缓解规则集列表每天从 Microsoft 威胁智能源更新多次，以便与自动程序保持同步。 即使自动攻击媒介发生变化，Web 应用程序也会不断受到保护。

## <a name="next-steps"></a>后续步骤

- [为 Azure 应用程序网关上的 Web 应用程序防火墙配置自动程序保护（预览）](bot-protection.md)
