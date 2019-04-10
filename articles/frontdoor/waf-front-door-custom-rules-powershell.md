---
title: 使用自定义规则和默认的花招集的第一道防线-Azure PowerShell 配置 web 应用程序防火墙 (WAF) 策略
description: 了解如何配置 WAF 策略包含自定义和托管现有的第一道防线终结点的规则。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362934"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>配置使用 Azure PowerShell 的 web 应用程序防火墙策略
Azure web 应用程序防火墙 (WAF) 策略定义在第一道防线的请求到达时所需的检查。
本文介绍如何配置由某些自定义规则和使用 Azure 托管默认的花招集启用了 WAF 策略。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件
在开始设置速率限制策略之前，设置 PowerShell 环境并创建第一道防线配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 在页上，若要使用 Azure 凭据，登录按照的说明，安装 Az PowerShell 模块。

#### <a name="sign-in-to-azure"></a>登录 Azure
```
Connect-AzAccount

```
在安装 Front Door 模块之前，请确保安装最新版本的 PowerShellGet。 运行下面的命令，重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>创建 Front Door 配置文件
遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建的第一道防线配置文件](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>基于 http 的参数的自定义规则

下面的示例演示如何使用来配置自定义规则使用的两个匹配条件[新建 AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject)。 请求是从指定的站点定义的引用网站，并且查询字符串不包含"password"。 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>根据 http 请求方法的自定义规则
创建阻止"PUT"方法使用的规则[新建 AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) ，如下所示：

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>创建自定义规则基于大小约束

以下示例创建阻止请求的长度超过 100 个字符使用 Azure PowerShell 的 Url 的规则：
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>添加托管默认规则集

以下示例创建的托管默认规则集使用 Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，配置安全策略与使用在前面步骤中创建的规则[新建 AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy)中包含的第一道防线配置文件的指定的资源组。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>链接到第一道防线前端主机的策略
安全策略对象链接到现有的第一道防线前端主机并更新第一道防线属性。 首先，检索第一道防线对象使用[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)。
接下来，设置前端*WebApplicationFirewallPolicyLink*属性设置为*resourceId*的使用上一步骤中创建的"$myWAFPolicy$"[集 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

以下示例使用资源组名称 *myResourceGroupFD1*，并假设已遵照以下文章中的说明创建了 Front Door 配置文件：[快速入门：创建 Front Door](quickstart-create-front-door.md)。 此外，在以下示例中，替换为 $frontDoorName 第一道防线配置文件的名称。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 只需设置*WebApplicationFirewallPolicyLink*属性一次安全策略链接到前端的第一道防线。 后续的策略更新自动应用于前端。

## <a name="next-steps"></a>后续步骤

- 详细了解[第一道防线](front-door-overview.md) 
- 详细了解[WAF 的第一道防线](waf-overview.md)
