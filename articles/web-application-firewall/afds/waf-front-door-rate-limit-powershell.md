---
title: 为前门配置 WAF 速率限制规则 - Azure 电源外壳
description: 了解如何为现有前门终结点配置速率限制规则。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649358"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>使用 Azure PowerShell 配置 Web 应用程序防火墙速率限制规则
Azure 前门的 Azure Web 应用程序防火墙 （WAF） 速率限制规则控制在一分钟内从客户端允许的请求数。
本文演示如何配置 WAF 速率限制规则，该规则控制从客户端允许的请求数到使用 Azure PowerShell 在 URL 中包含 */促销*的 Web 应用程序的请求数。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
在开始设置速率限制策略之前，请设置 PowerShell 环境并创建前门配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按照页面上的说明操作，使用 Azure 凭据登录，然后安装 Az PowerShell 模块。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用登录的交互式对话框连接到 Azure
```
Connect-AzAccount

```
安装前门模块之前，请确保安装了当前版本的 PowerShellGet。 运行以下命令并重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>创建 Front Door 配置文件
按照["快速入门：创建前门配置文件](../../frontdoor/quickstart-create-front-door.md)"中所述的说明创建前门配置文件

## <a name="define-url-match-conditions"></a>定义 URL 匹配条件
使用[New-AzFrontDoorWafMatch条件对象](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)定义 URL 匹配条件（URL 包含 /促销）。
以下示例将 */促销*匹配为*RequestUri*变量的值：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>创建自定义速率限制规则
使用[新阿兹门沃夫自定义规则对象](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)设置速率限制。 在下面的示例中，限制设置为 1000。 从任何客户端到促销页面在一分钟内超过 1000 的请求将被阻止，直到下一分钟开始。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzureRmResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，使用包含前门配置文件的指定资源组中[的 New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)配置具有自定义速率限制规则的安全策略。

下面的示例使用资源组名称*myResourceGroupFD1，* 假定您已使用["快速入门：创建前门"](../../frontdoor/quickstart-create-front-door.md)一文中提供的说明创建了前门配置文件。

 使用[新阿兹门沃夫政策](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>将策略链接到前门前端主机
将安全策略对象链接到现有的前门前端主机并更新前门属性。 首先使用[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)命令检索前门对象。
接下来，将前端 Web*应用程序防火墙策略链接*属性设置为使用[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令在上一步中创建的"$ratePolicy"*的资源 Id。* 

下面的示例使用资源组名称*myResourceGroupFD1，* 假定您已使用["快速入门：创建前门"](../../frontdoor/quickstart-create-front-door.md)一文中提供的说明创建了前门配置文件。 此外，在下面的示例中，将$frontDoorName替换为前门配置文件的名称。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需设置一次*Web 应用程序防火墙策略链接*属性，就将安全策略链接到前门前端。 后续策略更新将自动应用于前端。

## <a name="next-steps"></a>后续步骤

- 了解有关[前门](../../frontdoor/front-door-overview.md)的更多。 


