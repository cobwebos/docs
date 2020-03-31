---
title: 配置 WAF 自定义规则& Azure 前门的默认规则集
description: 了解如何配置 WAF 策略包含现有前门终结点的自定义和托管规则。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186635"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 应用程序防火墙策略

Azure Web 应用程序防火墙 （WAF） 策略定义请求到达前门时所需的检查。
本文演示如何配置由某些自定义规则和启用 Azure 管理的默认规则集的 WAF 策略。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

在开始设置速率限制策略之前，请设置 PowerShell 环境并创建前门配置文件。

### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境

Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按照页面上的说明操作，使用 Azure 凭据登录，然后安装 Az PowerShell 模块。

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

按照["快速入门：创建前门配置文件](../../frontdoor/quickstart-create-front-door.md)"中所述的说明创建前门配置文件

## <a name="custom-rule-based-on-http-parameters"></a>基于 http 参数的自定义规则

下面的示例演示如何使用[New-AzFrontDoorWafMatch条件对象](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)配置具有两个匹配条件的自定义规则。 请求来自引用者定义的指定站点，查询字符串不包含"密码"。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>基于 http 请求方法的自定义规则

使用[New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)创建阻止"PUT"方法的规则，如下所示：

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>基于大小约束创建自定义规则

下面的示例使用 Azure PowerShell 创建 Url 超过 100 个字符的规则阻止请求：
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>添加托管默认规则集

以下示例使用 Azure PowerShell 创建托管默认规则集：
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，使用包含前门配置文件的指定资源组中[的 New-AzFrontDoorWafPolicy，](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)在前面的步骤中使用创建的规则配置安全策略。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>将策略链接到前门前端主机

将安全策略对象链接到现有的前门前端主机并更新前门属性。 首先，使用[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)检索前门对象。
接下来，将前端 Web*应用程序防火墙策略链接*属性设置为使用[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)在上一步中创建的"$myWAFPolicy$"*的资源 ID。* 

下面的示例使用资源组名称*myResourceGroupFD1，* 假定您已使用["快速入门：创建前门"](../../frontdoor/quickstart-create-front-door.md)一文中提供的说明创建了前门配置文件。 此外，在下面的示例中，将$frontDoorName替换为前门配置文件的名称。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需设置一次*Web 应用程序防火墙策略链接*属性，就将安全策略链接到前门前端。 后续策略更新将自动应用于前端。

## <a name="next-steps"></a>后续步骤

- 了解有关[前门的更多](../../frontdoor/front-door-overview.md) 
- 了解有关[带前门的 WAF](afds-overview.md)的更多详细信息
