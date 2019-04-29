---
title: 配置 web 应用程序防火墙的自定义响应在 Azure 第一道防线
description: 了解如何配置自定义响应代码和消息，当 web 应用程序防火墙 (WAF) 阻止请求。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459770"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>配置 Azure web 应用程序防火墙的自定义响应

默认情况下，Azure web 应用程序防火墙 (WAF) 与 Azure 第一道防线因为匹配规则，阻止请求时它将返回 403 状态代码与**请求被阻塞**消息。 本文介绍如何配置自定义响应状态代码和响应消息时请求被阻塞的 WAF。

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

在 Azure 中，可将相关的资源分配到资源组。 通过使用在此示例中，创建资源组[新建 AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>创建新的 WAF 策略与自定义响应 

下面是使用自定义响应状态代码设置为 405 和消息创建新的 WAF 策略的一个示例**被阻止。** 使用[新建 AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy)。

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

修改自定义响应代码或响应正文设置的一个现有的 WAF 策略，使用[集 AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)。

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>后续步骤
- 详细了解[第一道防线](front-door-overview.md)