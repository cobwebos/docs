---
title: 为第一道防线-Azure PowerShell 配置 web 应用程序防火墙速率限制规则
description: 了解如何配置现有的第一道防线终结点的速率限制规则。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: 99b0cab3fd277f90a675f0e6087d572853053a08
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387337"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>配置 web 应用程序防火墙速率限制规则使用 Azure PowerShell
Azure web 应用程序防火墙 (WAF) 速率限制规则的 Azure 第一道防线控制一分钟持续期间允许从单个客户端 IP 的请求数。
本文介绍如何配置 WAF 速率限制规则，用于控制允许从单个客户端到包含的 web 应用程序的请求数 */promo*中使用 Azure PowerShell 的 URL。

> [!IMPORTANT]
> WAF 速率限制规则功能的 Azure 第一道防线当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件
在开始设置速率限制策略之前，设置 PowerShell 环境并创建第一道防线配置文件。
### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 在页上，若要使用 Azure 凭据，登录按照的说明，安装 Az PowerShell 模块。

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
遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建的第一道防线配置文件](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>定义 url 匹配条件
定义 （URL 包含 /promo） 的 URL 匹配条件使用[新建 AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)。
下面的示例匹配 */promo*的值作为*RequestUri*变量：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>创建自定义速率限制规则
设置速率限制使用[新建 AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)。 在以下示例中，该限制设置为 1000年。 在一个一分钟内超过 1000年的促销页面从任何客户端请求会被阻止，直到下一分钟启动。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>配置安全策略

使用 `Get-AzureRmResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，使用自定义速率限制规则使用配置安全策略[新建 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)中包含的第一道防线配置文件的指定的资源组。

以下示例使用资源组名称 *myResourceGroupFD1*，并假设已遵照以下文章中的说明创建了 Front Door 配置文件：[快速入门：创建 Front Door](quickstart-create-front-door.md)。

 使用[新建 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>链接到第一道防线前端主机的策略
安全策略对象链接到现有的第一道防线前端主机并更新第一道防线属性。 首先检索第一道防线对象使用[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)命令。
接下来，设置前端*WebApplicationFirewallPolicyLink*属性设置为*resourceId*的"$ratePolicy"在使用上一步骤中创建[集 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令。 

以下示例使用资源组名称 *myResourceGroupFD1*，并假设已遵照以下文章中的说明创建了 Front Door 配置文件：[快速入门：创建 Front Door](quickstart-create-front-door.md)。 此外，在以下示例中，替换为 $frontDoorName 第一道防线配置文件的名称。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 只需设置*WebApplicationFirewallPolicyLink*属性一次安全策略链接到前端的第一道防线。 后续的策略更新自动应用于前端。

## <a name="next-steps"></a>后续步骤

- 详细了解[第一道防线](front-door-overview.md) 


