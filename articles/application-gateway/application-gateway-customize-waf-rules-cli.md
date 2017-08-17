---
title: "自定义 Azure 应用程序网关的 Web 应用程序防火墙规则 - Azure CLI 2.0 | Microsoft Docs"
description: "此页提供有关如何使用 Azure CLI 2.0 自定义应用程序网关的 Web 应用程序防火墙规则的信息。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cfc01caf5c8ca11f790a6640ec57e3d14c1b7767
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>通过 Azure CLI 2.0 自定义 Web 应用程序防火墙规则

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

应用程序网关 Web 应用程序防火墙可为 Web 应用程序提供保护。 这些保护由 OWASP CRS 规则集提供。 某些规则可能会导致误报，并会阻止实际流量。  出于此原因，应用程序网关在启用了 Web 应用程序防火墙的应用程序网关上提供自定义规则组和规则的功能。 有关特定规则组和规则的详细信息，请访问 [Web 应用程序防火墙 CRS 规则组和规则](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>查看规则组和规则

以下示例演示如何查看可在启用了 WAF 的应用程序网关上配置的规则和规则组。

### <a name="view-rule-groups"></a>查看规则组

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

下面是截取自前一示例的响应。

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>查看规则组中的规则

以下示例演示如何查看指定的规则组中的规则。

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

以下输出是截取自前一示例的响应。

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>禁用规则

以下示例在应用程序网关上禁用规则 `910018` 和 `910017`。

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>后续步骤

配置已禁用的规则后，可通过访问[应用程序网关诊断](application-gateway-diagnostics.md#diagnostic-logging)来了解如何查看 WAF 日志

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
