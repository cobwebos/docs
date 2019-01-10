---
title: 在现有 Azure 应用程序网关中重写 HTTP 标头
description: 本文提供有关如何使用 Azure PowerShell 重写现有 Azure 应用程序网关中的 HTTP 标头的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 8429618a945ec70c52b925887790aa469e23ef89
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729904"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>在现有应用程序网关中重写 HTTP 标头

可以使用 Azure PowerShell 在现有的[自动缩放和区域冗余的应用程序网关 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 中配置[重写 HTTP 请求和响应标头的规则](rewrite-http-headers.md)。

> [!IMPORTANT] 
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 检索现有的应用程序网关配置
> * 指定 http 标头重写规则配置
> * 使用上述配置更新应用程序网关以重写 http 标头

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程要求在本地运行 Azure PowerShell。 必须安装 Az 模块 1.0.0 或更高版本。 依次运行 `Import-Module Az` 和 `Get-Module Az` 以查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 验证 PowerShell 版本以后，请运行 `Login-AzAccount`，以便创建与 Azure 的连接。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**指定 http 标头重写规则配置**

配置重写 http 标头所需的新对象：

- **RequestHeaderConfiguration**：此对象用于指定要重写的请求标头字段以及需要重写的原始标头的新值。
- **ResponseHeaderConfiguration**：此对象用于指定要重写的响应标头字段以及需要重写的原始标头的新值。
- **ActionSet**：此对象包含上面指定的请求和响应标头的配置。 
- **RewriteRule**：此对象包含上面指定的所有“actionSets”。 
- **RewriteRuleSet** - 此对象包含所有“rewriteRules”，并且需要附加到（基本或基于路径的）请求路由规则。

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>检索现有的应用程序网关配置

```azurepowershell
$appgw1 = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>检索现有的请求路由规则配置

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用配置更新应用程序网关以重写 http 标头

```azurepowershell
$appgw1 = Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
$appgw2 = Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
$appgw3 =  Set-AzApplicationGateway -ApplicationGateway $appgw2
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用基于 URL 路径的路由规则创建应用程序网关](./tutorial-url-route-powershell.md)
