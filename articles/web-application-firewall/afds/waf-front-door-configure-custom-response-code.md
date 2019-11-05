---
title: 使用 Azure 前门为 Web 应用程序防火墙配置自定义响应
description: 了解如何在 Web 应用程序防火墙（WAF）阻止请求时配置自定义响应代码和消息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: ae3099373bbaece57c2896eb0fb4e7d59d817f25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517145"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>配置 Azure Web 应用程序防火墙的自定义响应

默认情况下，当使用 Azure 前端的 Azure Web 应用程序防火墙（WAF）阻止请求，因为存在匹配的规则时，会返回一个403状态代码，其中包含**请求被阻止**的消息。 本文介绍如何在 WAF 阻止请求时配置自定义响应状态代码和响应消息。

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

在 Azure 中，可将相关的资源分配到资源组。 在此示例中，将使用[AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)创建资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>使用自定义响应创建新的 WAF 策略 

下面是一个示例，介绍如何创建新的 WAF 策略，并将自定义响应状态代码设置为405，并阻止发送消息 **。** 使用[AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

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

使用[AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)修改现有 WAF 策略的自定义响应代码或响应正文设置。

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
- 了解有关[Azure 前门的 Web 应用程序防火墙的](../afds/afds-overview.md)详细信息