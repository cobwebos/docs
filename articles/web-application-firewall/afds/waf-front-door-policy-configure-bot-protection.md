---
title: 使用 Azure 前门配置 Web 应用程序防火墙的自动程序保护（预览版）
description: 了解 Web 应用程序防火墙 （WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: f48b683044bc727cda461fb64a743c055188962a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934658"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>为 Web 应用程序防火墙配置自动程序保护（预览）
本文介绍如何使用 Azure 门户在 Azure Web 应用程序防火墙 （WAF） 中为前门配置自动程序保护规则。 还可以使用 CLI、Azure PowerShell 或 Azure 资源管理器模板配置自动程序保护规则。

> [!IMPORTANT]
> 机器人防护规则集当前为公共预览版，并提供预览版服务级别协议。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

使用 Azure 门户，按照 Azure[前门创建 WAF 策略](waf-front-door-create-portal.md)中描述的说明，为前门创建基本 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用自动程序保护规则集

在创建 Web 应用程序防火墙策略时的 **"托管规则"** 页中，首先查找**托管规则集**部分，从下拉菜单中选择规则**Microsoft_BotManager_1.0**前面的复选框，然后选择"审阅 **+ 创建**"。

   ![机器人保护规则](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>后续步骤

- 了解如何监视[WAF](waf-front-door-monitor.md)。
