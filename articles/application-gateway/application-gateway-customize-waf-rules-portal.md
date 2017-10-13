---
title: "自定义 Azure 应用程序网关的 Web 应用程序防火墙规则 - Azure 门户 | Microsoft 文档"
description: "本文将介绍如何使用 Azure 门户自定义应用程序网关的 Web 应用程序防火墙规则。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 406e491aa54806b3534ef0f500d6aea03434e2c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户自定义 Web 应用程序防火墙规则

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Azure 应用程序网关 Web 应用程序防火墙 (WAF) 可为 Web 应用程序提供保护。 这些保护通过打开 Web 应用程序安全性项目 (OWASP) 核心规则集 (CRS) 来提供。 某些规则可能会导致误报，并会阻止实际流量。 出于此原因，应用程序网关提供了自定义规则组和规则的功能。 有关特定规则组和规则的详细信息，请参阅 [Web 应用程序防火墙 CRS 规则组和规则列表](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果应用程序网关未使用 WAF 层，会在右侧窗格中显示“将应用程序网关升级到 WAF 层”选项。 

![启用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>查看规则组和规则

**查看规则组和规则**
   1. 浏览到应用程序网关并选择“Web 应用程序防火墙”。  
   2. 选择“高级规则配置”。  
   此视图会在随所选规则集提供的所有规则组页上显示一个表。 已选中所有规则的复选框。

![配置已禁用的规则][1]

## <a name="search-for-rules-to-disable"></a>搜索要禁用的规则

“Web 应用程序防火墙设置”边栏选项卡提供了通过文本搜索筛选规则的功能。 结果仅显示包含所搜索的文本的规则组和规则。

![搜索规则][2]

## <a name="disable-rule-groups-and-rules"></a>禁用规则组和规则

禁用规则时可以禁用整个规则组，也可以禁用一个或多个规则组下的特定规则。 

**禁用规则组或特定规则**

   1. 搜索想要禁用的规则或规则组。
   2. 取消选中与要禁用的规则对应的复选框。 
   2. 选择“保存”。 

![保存更改][3]

## <a name="next-steps"></a>后续步骤

配置你禁用的规则后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
