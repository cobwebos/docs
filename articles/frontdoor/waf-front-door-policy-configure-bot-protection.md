---
title: 配置与 Azure 第一道防线 （预览版） 的智能机器人应用程序保护的 web 应用程序防火墙
description: 了解 web 应用程序防火墙 (WAF)。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481619"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>配置智能机器人应用程序保护的 web 应用程序防火墙 （预览版）
本文介绍如何使用 Azure CLI、 Azure PowerShell 或 Azure 资源管理器模板，为第一道防线配置智能机器人应用程序防护规则 Azure web 应用程序防火墙 (WAF)。

Waf 对来自已知的恶意 IP 地址的请求执行自定义操作，可以启用托管的智能机器人应用程序保护规则集。 IP 地址源自 Microsoft 威胁智能源。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)为 Microsoft 威胁智能提供支持，并由多个服务，包括 Azure 安全中心。

> [!IMPORTANT]
> 智能机器人应用程序保护规则集目前处于公共预览状态，并提供预览版服务级别协议。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备组件

第一道防线中所述的说明创建一个基本的 WAF 策略[使用 Azure 门户创建 WAF 策略用于 Azure 第一道防线](waf-front-door-create-portal.md)。

## <a name="enable-bot-protection-rule-set"></a>启用智能机器人应用程序保护规则集

1. 在基本策略创建的页面，您在前面部分中下,**设置**，单击**规则**。
2. 在详细信息页中下,**管理规则**部分中，从下拉列表菜单中，选择前面的规则的复选框**BotProtection 预览 0.1**，然后选择**保存**上面。
    
   ![智能机器人应用程序防护规则](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[监视 WAF](waf-front-door-monitor.md)。
