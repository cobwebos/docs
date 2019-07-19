---
title: 使用自定义规则配置 web 应用程序防火墙 (WAF) 策略, 并为前门 Azure PowerShell 设置默认 Ruse
description: 了解如何配置 WAF 策略, 同时包含现有前门终结点的自定义和托管规则。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: e9509172ac96a601235cc16e0d6d83c9b2f51902
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849122"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>使用 Azure PowerShell 配置 web 应用程序防火墙策略
Azure web 应用程序防火墙 (WAF) 策略定义请求到达前门时所需的检查。
本文介绍如何配置一个 WAF 策略, 其中包含一些自定义规则和启用了 Azure 托管的默认 Ruse 集。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
在开始设置速率限制策略之前, 请设置 PowerShell 环境, 并创建前门配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按照页面上的说明, 用 Azure 凭据登录, 并安装 Az PowerShell 模块。

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
遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建前门配置文件](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>基于 http 参数的自定义规则

下面的示例演示如何使用[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)配置具有两个匹配条件的自定义规则。 请求来自于引用方定义的指定站点, 并且查询字符串不包含 "password"。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>基于 http 请求方法的自定义规则
使用[AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)创建规则阻止 "PUT" 方法, 如下所示:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>基于大小约束创建自定义规则

下面的示例使用 Azure PowerShell 创建 Url 长度超过100个字符的规则阻止请求:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>添加托管默认规则集

下面的示例使用 Azure PowerShell 创建托管默认规则集:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来, 在前面步骤中使用[AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)在包含前门配置文件的指定资源组中配置安全策略和创建的规则。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>将策略链接到前门前端主机
将安全策略对象链接到现有前门前端主机并更新前门属性。 首先, 使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)检索前门对象。
接下来, 使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)将前端*WebApplicationFirewallPolicyLink*属性设置为在上一步中创建的 "$MyWAFPolicy $" 的*resourceId* 。 

以下示例使用资源组名称 *myResourceGroupFD1*，并假设已遵照以下文章中的说明创建了 Front Door 配置文件：[快速入门：创建 Front Door](quickstart-create-front-door.md)。 此外, 在下面的示例中, 将 $frontDoorName 替换为前门配置文件的名称。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 只需设置*WebApplicationFirewallPolicyLink*属性一次, 即可将安全策略链接到前门前端。 后续策略更新会自动应用到前端。

## <a name="next-steps"></a>后续步骤

- 了解有关[前门](front-door-overview.md)的详细信息 
- 了解有关[WAF 的](waf-overview.md)详细信息
