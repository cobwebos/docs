---
title: 用 Azure 前门为 WAF 配置机器人保护（预览版）
description: 了解 Web 应用程序防火墙（WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186701"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>为 Web 应用程序防火墙配置机器人保护（预览版）
本文介绍如何使用 Azure CLI、Azure PowerShell 或 Azure 资源管理器模板为前门配置 Azure Web 应用程序防火墙（WAF）中的机器人保护规则。

可为 WAF 启用托管机器人防护规则集，以便针对来自已知恶意 IP 地址的请求采取自定义操作。 IP 地址源自 Microsoft 威胁智能源。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) 为 Microsoft 威胁智能助力，它已得到 Azure 安全中心等多项服务的运用。

> [!IMPORTANT]
> 机器人防护规则集当前以公共预览版提供，并随预览版服务级别协议一起提供。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

按照[使用 Azure 门户为 Azure 前门创建 WAF 策略](waf-front-door-create-portal.md)中所述的说明，为前门创建基本的 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用机器人保护规则集

1. 在上一部分中创建的基本策略页的 "**设置**" 下，单击 "**规则**"。
2. 在 "详细信息" 页的 "**管理规则**" 部分下，从下拉菜单中选中 "规则**BotProtection-0.1**" 前面的复选框，然后选择 "上**保存**"。
    
   ![机器人保护规则](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[监视 WAF](waf-front-door-monitor.md)。
