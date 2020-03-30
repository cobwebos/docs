---
title: 使用 Azure 前门配置 WAF 的自定义响应
description: 了解如何在 Web 应用程序防火墙 （WAF） 阻止请求时配置自定义响应代码和消息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185349"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>为 Azure Web 应用程序防火墙配置自定义响应

默认情况下，当 Azure Web 应用程序防火墙 （WAF） 与 Azure 前门由于匹配的规则而阻止请求时，它将返回一个 403 状态代码，该**请求被阻止**消息。 本文介绍当 WAF 阻止请求时，如何配置自定义响应状态代码和响应消息。

## <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 遵照页面上的说明使用 Azure 凭据登录，并安装 Az PowerShell 模块。

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

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 在此示例中，使用[New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)创建资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>使用自定义响应创建新的 WAF 策略 

下面是创建新的 WAF 策略的示例，自定义响应状态代码设置为 405，**并且将阻止**您收到消息。 使用[新阿兹门沃夫政策](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

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

使用[Update-AzFrontDoorFireWall 策略](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)修改现有 WAF 策略的自定义响应代码或响应正文设置。

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
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>后续步骤
- 使用 Azure 前门了解有关[Web 应用程序防火墙的信息](../afds/afds-overview.md)