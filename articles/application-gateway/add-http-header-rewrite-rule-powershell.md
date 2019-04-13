---
title: 在 Azure 应用程序网关中重写 HTTP 标头
description: 本文介绍如何重写 HTTP 标头中 Azure 应用程序网关使用 Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: bfafc74cbcb97f28cc085196a2cbaf4e9bf2e871
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548313"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>重写 HTTP 请求和响应标头与 Azure 应用程序网关-Azure PowerShell

本文介绍如何使用 Azure PowerShell 来配置[应用程序网关 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)重写中的请求和响应的 HTTP 标头。

> [!IMPORTANT]
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

- 本教程要求在本地运行 Azure PowerShell。 必须安装 Az 模块 1.0.0 或更高版本。 依次运行 `Import-Module Az` 和 `Get-Module Az` 以查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。 验证 PowerShell 版本以后，请运行 `Login-AzAccount`，以便创建与 Azure 的连接。
- 需要 v1 SKU 不支持 SKU，因为标头重写功能的应用程序网关 v2。 如果您没有 v2 SKU，创建[应用程序网关 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>)在开始之前。

## <a name="what-is-required-to-rewrite-a-header"></a>重写一个标头的要求

若要配置 HTTP 标头重写，将需要：

1. 创建重写 http 标头所需的新对象：

   - **RequestHeaderConfiguration**：此对象用于指定要重写的请求标头字段以及需要重写的原始标头的新值。

   - **ResponseHeaderConfiguration**：此对象用于指定要重写的响应标头字段以及需要重写的原始标头的新值。

   - **ActionSet**：此对象包含上面指定的请求和响应标头的配置。

   - **条件**：它是一项可选配置。 如果添加重写条件，则会评估的 HTTP (S) 请求和响应的内容。 是否使用重写的条件匹配的 HTTP (S) 请求或响应，将基于执行与重写条件关联的重写操作的决策。 

     如果与之关联多个条件将执行操作，则该操作，仅当满足所有条件时，即，将执行逻辑与运算。

   - **RewriteRule**： 包含多个重写操作的重写条件组合。

   - **RuleSequence**:这是一项可选配置。 它可帮助确定在其中执行不同的重写规则的顺序。 当有多个重写规则集中重写时，这非常有用。 获取首先执行具有较低的规则序列值的重写规则。 如果你提供两个重写规则的相同规则序列的执行顺序将非确定性。

     如果未显式指定 RuleSequence，将设置默认值为 100。

   - **RewriteRuleSet**： 此对象包含多个重写规则将关联到的请求路由规则。

2. 你将需要附加 rewriteRuleSet 与路由规则。 这是因为重写配置已附加到源侦听器通过路由规则。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 因此，它仅适用于站点的特定路径区域。

可以创建多个 http 标头重写集，并且每个重写集可应用于多个侦听器。 不过，您可以应用仅其中一个重写设置为特定的侦听程序。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**指定 http 标头重写规则配置**

在此示例中，我们将通过重写在 http 响应的 location 标头，当位置标头中包含对"azurewebsites.net"的引用时修改重定向 URL。 若要执行此操作，我们将添加一个条件，用于评估是否在响应中的 location 标头包含的使用模式的 azurewebsites.net `(https?):\/\/.*azurewebsites\.net(.*)$`。 我们将使用`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值。 这将替换*azurewebsites.net*与*contoso.com* location 标头中。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>检索现有的应用程序网关配置

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>检索现有的请求路由规则配置

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用配置更新应用程序网关以重写 http 标头

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>删除重写规则

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>后续步骤

若要了解有关所需完成一些常见的配置的详细使用情况下，请参阅[常见标头重写方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。