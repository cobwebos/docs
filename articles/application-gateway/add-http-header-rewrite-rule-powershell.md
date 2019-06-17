---
title: 在 Azure 应用程序网关中重写 HTTP 标头
description: 本文提供有关如何使用 Azure PowerShell 重写在 Azure 应用程序网关的 HTTP 标头信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947192"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>重写 HTTP 请求和响应标头与 Azure 应用程序网关-Azure PowerShell

本文介绍如何使用 Azure PowerShell 来配置[应用程序网关 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)实例重写中请求和响应的 HTTP 标头。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

- 需要运行 Azure PowerShell 本地完成这篇文章中的步骤。 您还需要具有 Az 模块版本 1.0.0 或更高版本安装。 运行`Import-Module Az`，然后`Get-Module Az`来确定已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。 验证 PowerShell 版本以后，请运行 `Login-AzAccount`，以便创建与 Azure 的连接。
- 需要有一个应用程序网关 v2 SKU 实例。 在 v1 SKU 中不支持重写标头。 如果您没有 v2 SKU，创建[应用程序网关 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)实例在开始之前。

## <a name="create-required-objects"></a>创建所需的对象

若要配置 HTTP 标头重写时，需要完成以下步骤。

1. 创建所需的 HTTP 标头重写的对象：

   - **RequestHeaderConfiguration**:用于指定你想要重写的请求标头字段和标头的新值。

   - **ResponseHeaderConfiguration**:用于指定你想要重写的响应标头字段和标头的新值。

   - **ActionSet**:包含以前指定的请求和响应标头的配置。

   - **条件**：一项可选配置。 重写条件评估结果的 HTTP (S) 请求和响应的内容。 如果 HTTP (S) 请求或响应与重写条件匹配时，将发生的重写操作。

     如果将多个条件与操作相关联，仅当满足所有条件时，则会发生操作。 换而言之，该操作是逻辑 AND 运算。

   - **RewriteRule**:包含多个重写操作 / 重写条件组合。

   - **RuleSequence**:一个可选配置，可以帮助您确定的顺序重写规则执行。 重写集中有多个重写规则时，此配置非常有用。 首先运行具有较低的规则顺序值的重写规则。 如果将相同的规则序列值分配到两个重写规则，请执行顺序是不确定的。

     如果未显式指定 RuleSequence，设置默认值为 100。

   - **RewriteRuleSet**:包含将请求路由规则相关联的多个重写规则。

2. 将 RewriteRuleSet 附加到的路由规则。 重写配置附加到源侦听器通过路由规则。 当您使用基本的路由规则时，标头重写配置与源侦听器相关联，并全局标头重写。 当使用基于路径的路由规则时，URL 路径映射定义标头重写配置。 在这种情况下，它仅适用于站点的特定路径区域。

您可以创建多个 HTTP 标头重写集和应用设置多个侦听器为每个重写。 但您可以将应用仅其中一个重写设置为特定的侦听程序。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>指定的 HTTP 标头重写规则配置

在此示例中，我们将重写在 HTTP 响应的 location 标头时的 location 标头包含对 azurewebsites.net 的引用来修改重定向 URL。 若要执行此操作，我们将添加一个条件，用于评估是否在响应中的 location 标头包含 azurewebsites.net。 我们将使用该模式`(https?):\/\/.*azurewebsites\.net(.*)$`。 我们将使用和`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值。 此值将替换*azurewebsites.net*与*contoso.com* location 标头中。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>检索应用程序网关的配置

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>检索你的请求路由规则配置

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用重写 HTTP 标头的配置更新应用程序网关

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

若要了解有关如何设置一些常见用例的详细信息，请参阅[常见标头重写方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。