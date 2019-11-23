---
title: 为 Azure Web 应用程序防火墙配置机器人保护（WAF）
description: 了解如何在 Azure 应用程序网关上为 Web 应用程序防火墙（WAF）配置机器人保护。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516859"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>为 Azure 应用程序网关（预览版）上的 Web 应用程序防火墙配置机器人保护

本文介绍如何使用 Azure 门户在 Azure Web 应用程序防火墙（WAF）中为应用程序网关配置机器人保护规则。 

可以为 WAF 启用托管机器人保护规则集，以便阻止或记录来自已知恶意 IP 地址的请求。 IP 地址源自 Microsoft 威胁智能源。 Intelligent Security Graph 为 Microsoft 威胁智能助力，它已得到 Azure Security Center 等多项服务的运用。

> [!NOTE]
> 机器人保护规则集当前以公共预览版提供，并随预览版服务级别协议一起提供。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 。

## <a name="prerequisites"></a>先决条件

按照 [创建应用程序网关的 Web 应用程序防火墙策略](create-waf-policy-ag.md)中所述的说明，为应用程序网关创建基本的 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用机器人保护规则集

1. 在之前创建的**基本**策略页中的 "**设置**" 下，选择 "**规则**"。  

2. 在 "详细信息" 页的 " **管理规则** " 部分下，从下拉菜单中选中 "机器人保护规则" 复选框，然后选择 "**保存**"。

> [!div class="mx-imgBorder"]
> ![机器人保护](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>后续步骤

有关自定义规则的详细信息，请参阅[Azure 应用程序 Gateway 上的 Web 应用程序防火墙 v2 的自定义规则](custom-waf-rules-overview.md)。