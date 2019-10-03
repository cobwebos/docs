---
title: 通过 Azure 前门为 web 应用程序防火墙配置机器人保护 (预览版)
description: 了解 web 应用程序防火墙 (WAF)。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 0f36ea33badad52c55cd11491874db955df5408b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846345"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>为 web 应用程序防火墙配置机器人保护 (预览版)
本文介绍如何使用 Azure CLI、Azure PowerShell 或 Azure 资源管理器模板为前门配置 Azure web 应用程序防火墙 (WAF) 中的机器人保护规则。

可以为 WAF 启用托管机器人保护规则集, 以对来自已知恶意 IP 地址的请求执行自定义操作。 IP 地址源自 Microsoft 威胁情报源。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)为 Microsoft 威胁智能提供技术支持, 由多种服务 (包括 Azure 安全中心) 使用。

> [!IMPORTANT]
> 机器人保护规则集当前以公共预览版提供, 并随预览版服务级别协议一起提供。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

按照[使用 Azure 门户为 Azure 前门创建 WAF 策略](waf-front-door-create-portal.md)中所述的说明, 为前门创建基本的 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用机器人保护规则集

1. 在上一部分中创建的基本策略页的 "**设置**" 下, 单击 "**规则**"。
2. 在 "详细信息" 页的 "**管理规则**" 部分下, 从下拉菜单中选中 "规则**BotProtection-0.1**" 前面的复选框, 然后选择 "上**保存**"。
    
   ![机器人保护规则](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[监视 WAF](waf-front-door-monitor.md)。
