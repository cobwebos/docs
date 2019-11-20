---
title: 为前门配置 WAF rate 限制规则-Azure PowerShell
description: 了解如何为现有前门终结点配置速率限制规则。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 831d0876c67aa36248a54f3935e5ce7884c736ef
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186629"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 应用程序防火墙速率限制规则
Azure 前门的 Azure Web 应用程序防火墙（WAF）速率限制规则控制单个客户端 IP 在一分钟的时间内允许的请求数。
本文介绍如何配置 WAF rate 限制规则，该规则使用 Azure PowerShell 控制从单个客户端到包含 URL 中的 */promo*的 web 应用程序的请求数。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
在开始设置速率限制策略之前，请设置 PowerShell 环境，并创建前门配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按照页面上的说明，用 Azure 凭据登录，并安装 Az PowerShell 模块。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用交互式登录对话框连接到 Azure
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
按照[快速入门：创建前门配置文件](../../frontdoor/quickstart-create-front-door.md)中所述的说明创建前门配置文件

## <a name="define-url-match-conditions"></a>定义 url 匹配条件
使用[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)定义 url 匹配条件（url 包含/promo）。
下面的示例将 */promo*与*RequestUri*变量的值进行匹配：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>创建自定义速率限制规则
使用[AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)设置速率限制。 在下面的示例中，此限制设置为1000。 在一分钟内，从任何客户端到促销1000页的请求都将被阻止，直到下一分钟开始。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzureRmResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，使用包含前门配置文件的指定资源组中的[AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)配置具有自定义速率限制规则的安全策略。

下面的示例使用资源组名称*myResourceGroupFD1* ，假设已使用[快速入门：创建前门](../../frontdoor/quickstart-create-front-door.md)文章中提供的说明创建了前门配置文件。

 使用[AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>将策略链接到前门前端主机
将安全策略对象链接到现有前门前端主机并更新前门属性。 首先，使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)命令检索前门对象。
接下来[，使用 AzFrontDoor 命令将前端](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) *WebApplicationFirewallPolicyLink*属性设置为在上一步中创建的 "$ratePolicy" 的*resourceId* 。 

下面的示例使用资源组名称*myResourceGroupFD1* ，假设已使用[快速入门：创建前门](../../frontdoor/quickstart-create-front-door.md)文章中提供的说明创建了前门配置文件。 此外，在下面的示例中，将 $frontDoorName 替换为前门配置文件的名称。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 只需设置*WebApplicationFirewallPolicyLink*属性一次，即可将安全策略链接到前门前端。 后续策略更新会自动应用到前端。

## <a name="next-steps"></a>后续步骤

- 详细了解[前门](../../frontdoor/front-door-overview.md)。 


