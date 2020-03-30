---
title: 为 Azure Web 应用程序防火墙 （WAF） 配置自动程序保护
description: 了解如何在 Azure 应用程序网关上为 Web 应用程序防火墙 （WAF） 配置自动程序保护。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516859"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>为 Azure 应用程序网关上的 Web 应用程序防火墙配置自动程序保护（预览）

本文介绍如何使用 Azure 门户为应用程序网关配置 Azure Web 应用程序防火墙 （WAF） 中的自动程序保护规则。 

您可以为 WAF 启用托管自动程序保护规则集，以阻止或记录来自已知恶意 IP 地址的请求。 IP 地址源自 Microsoft 威胁智能源。 Intelligent Security Graph 为 Microsoft 威胁智能助力，它已得到 Azure Security Center 等多项服务的运用。

> [!NOTE]
> 自动程序保护规则集当前处于公共预览版中，并且提供预览服务级别协议。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 Microsoft Azure  [预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

按照为 [应用程序网关创建 Web 应用程序防火墙策略](create-waf-policy-ag.md)中描述的说明，为应用程序网关创建基本的 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用自动程序保护规则集

1. 在以前创建**的基本**策略页中，在 **"设置"** 下，选择 **"规则**"。  

2. 在详细信息页中，在 **"管理规则"** 部分下，从下拉菜单中选择"自动保护"规则的复选框，然后选择"**保存**"。

> [!div class="mx-imgBorder"]
> ![机器人防护](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>后续步骤

有关自定义规则的详细信息，请参阅[Azure 应用程序网关上的 Web 应用程序防火墙 v2 的自定义规则](custom-waf-rules-overview.md)。