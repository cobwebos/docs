---
title: 用 Azure 前门为 Web 应用程序防火墙 (WAF) 配置自定义响应
description: 了解如何在 WAF 阻止请求时配置自定义响应代码和消息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: a995460793686d8293d77965e74e2cbf916925a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005593"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>配置 Azure Web 应用程序防火墙 (WAF 的自定义响应) 

默认情况下，当 WAF 由于匹配规则而阻止请求时，它将返回403状态代码， **请求被阻止** 消息。 默认消息还包括可用于链接到请求的 [日志条目](https://docs.microsoft.com/azure/web-application-firewall/afds/waf-front-door-monitor) 的跟踪引用字符串。  您可以使用用例的引用字符串来配置自定义响应状态代码和自定义消息。 本文介绍如何在 WAF 阻止请求时配置自定义响应页面。

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>配置自定义响应状态代码和消息使用门户

可以从 WAF 门户中的 "策略设置" 下配置自定义响应状态代码和正文。

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF 策略设置&quot;:::

在上面的示例中，我们将响应代码保存为403，并配置一个简短的 &quot;联系我们" 消息，如下图所示：

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="WAF 策略设置&quot;:::

在上面的示例中，我们将响应代码保存为403，并配置一个简短的 &quot;联系我们" 在响应正文中插入唯一引用字符串。 此值与和日志中的 TrackingReference 字段匹配 `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` 。

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>使用 PowerShell 配置自定义响应状态代码和消息

### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境

Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 并在任何 PowerShell 会话中使用它。 遵照页面上的说明使用 Azure 凭据登录，并安装 Az PowerShell 模块。

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用交互式登录对话框连接到 Azure

```
Connect-AzAccount
Install-Module -Name Az

```
确保已安装 PowerShellGet 最新版本。 运行下面的命令，重新打开 PowerShell。
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 在这里，我们将使用 [AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)创建一个资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>使用自定义响应创建新的 WAF 策略 

下面的示例创建了一个新的 WAF 策略，并将自定义响应状态代码设置为405，并 **阻止了向你**发送的消息。使用 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

使用 [AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)修改现有 WAF 策略的自定义响应代码或响应正文设置。

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>后续步骤
- 了解有关[Azure 前门的 Web 应用程序防火墙的](../afds/afds-overview.md)详细信息