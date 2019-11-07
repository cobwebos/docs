---
title: Azure PowerShell 脚本示例 - 创建 WAF 自定义规则
description: Azure PowerShell 脚本示例 - 在应用程序网关上创建 Web 应用程序防火墙的自定义规则
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 950f71c284268a9aa2773eb57213e266622d85bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509551"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>使用 Azure PowerShell 创建 WAF 自定义规则

此脚本将创建使用自定义规则的应用程序网关 Web 应用程序防火墙。 如果请求标头包含用户代理 *evilbot*，该自定义规则会阻止流量。

## <a name="prerequisites"></a>先决条件

### <a name="azure-powershell-module"></a>Azure PowerShell 模块

如果选择在本地安装并使用 Azure PowerShell，则此脚本需要安装 Azure PowerShell 模块 2.1.0 或更高版本。

1. 要查找版本，请运行 `Get-Module -ListAvailable Az`。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。
2. 若要创建与 Azure 的连接，请运行 `Connect-AzAccount`。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、应用程序网关和所有相关资源。

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建子网配置。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 使用子网配置创建虚拟网络。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建应用程序网关的公用 IP 地址。 |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | 创建将子网与应用程序网关相关联的配置。 |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | 创建为应用程序网关分配公用 IP 地址的配置。 |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | 分配用于访问应用程序网关的端口。 |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | 创建应用程序网关的后端池。 |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | 配置后端池的设置。 |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | 创建侦听器。 |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | 创建路由规则。 |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | 指定应用程序网关的层和容量。 |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | 创建应用程序网关。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|为应用程序网关创建自动缩放配置。|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|为防火墙条件创建匹配变量。|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|为自定义规则创建匹配条件。|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|为应用程序网关防火墙策略创建新的自定义规则。|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|创建应用程序网关防火墙策略。|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|创建应用程序网关的 WAF 配置。|

## <a name="next-steps"></a>后续步骤

- 有关 WAF 自定义规则的详细信息，请参阅 [Web 应用程序防火墙的自定义规则](../ag/custom-waf-rules-overview.md)
- 有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。
