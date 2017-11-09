---
title: "自定义 Azure 应用程序网关的 Web 应用程序防火墙规则 - PowerShell | Microsoft Docs"
description: "本文将介绍如何使用 PowerShell 自定义应用程序网关的 Web 应用程序防火墙规则。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 97c3fe6f0b7a4d9b967b44bf819a6f25598febc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>通过 PowerShell 自定义 Web 应用程序防火墙规则

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Azure 应用程序网关 Web 应用程序防火墙 (WAF) 可为 Web 应用程序提供保护。 这些保护通过打开 Web 应用程序安全性项目 (OWASP) 核心规则集 (CRS) 来提供。 某些规则可能会导致误报，并会阻止实际流量。 出于此原因，应用程序网关提供了自定义规则组和规则的功能。 有关特定规则组和规则的详细信息，请参阅 [Web 应用程序防火墙 CRS 规则组和规则列表](application-gateway-crs-rulegroups-rules.md)。

## <a name="view-rule-groups-and-rules"></a>查看规则组和规则

以下代码示例演示了如何查看可在启用了 WAF 的应用程序网关上配置的规则和规则组。

### <a name="view-rule-groups"></a>查看规则组

以下示例演示了如何查看规则组：

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

以下输出截取自前一示例的响应：

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>禁用规则

以下示例在应用程序网关上禁用了规则 `910018` 和 `910017`：

```azurecli
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>后续步骤

配置你禁用的规则后，可以了解如何查看 WAF 日志。 有关详细信息，请参阅[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
