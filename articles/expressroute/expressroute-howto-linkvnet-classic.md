---
title: 将虚拟网络链接到 ExpressRoute 线路：PowerShell：经典：Azure | Microsoft 文档
description: 本文档概述如何使用经典部署模型和 PowerShell 将虚拟网络 (VNet) 链接到 ExpressRoute 线路。
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: 10b623947b6e776c4f8f41e8424262d7f2a3e933
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343369"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>使用 PowerShell 将虚拟网络连接到 ExpressRoute 线路（经典）
> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
>

本文介绍如何使用 PowerShell 将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 单个 VNet 可最多连接到 4 条 ExpressRoute 线路。 通过本文中的步骤为正在连接的每条 ExpressRoute 线路创建新链接。 ExpressRoute 线路可在同一订阅、不同订阅或两者兼有。 本文适用于使用经典部署模型创建的虚拟网络。

最多可以将 10 个虚拟网络链接到一条 ExpressRoute 线路。 所有虚拟网络必须都位于同一地缘政治区域。 如果启用 ExpressRoute 高级外接程序，则可以将更多虚拟网络链接到 ExpressRoute 线路，或者链接其他地缘政治区域中的虚拟网络。 有关高级外接程序的更多详细信息，请参阅[常见问题解答](expressroute-faqs.md)。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>配置先决条件

* 在开始配置之前，请先查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。
* 必须有一个活动的 ExpressRoute 线路。
   * 请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)，并让连接提供商启用该线路。
   * 请确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](expressroute-howto-routing-classic.md)一文。
   * 确保配置 Azure 专用对等互连并运行网络和 Microsoft 之间的 BGP 对等互连，以便启用端到端连接。
   * 必须已创建并完全预配一个虚拟网络和一个虚拟网络网关。 请按说明[为 ExpressRoute 配置虚拟网络](expressroute-howto-vnet-portal-classic.md)。

### <a name="download-the-latest-powershell-cmdlets"></a>下载最新的 PowerShell cmdlet

安装最新版本的 Azure 服务管理 (SM) PowerShell 模块和 ExpressRoute 模块。 使用以下示例时，请注意，当更新版本的 cmdlet 发布时，版本号（在此示例中为 5.1.1）将更改。

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

如果需要有关 Azure PowerShell 的更多信息，请参阅 [Azure PowerShell cmdlet 入门](/powershell/azure/overview)来获取有关如何配置计算机以使用 Azure PowerShell 模块的分步指导。

### <a name="sign-in"></a>登录

若要登录到 Azure 帐户，请使用以下示例：

1. 使用提升的权限打开 PowerShell 控制台，并连接到帐户。

  ```powershell
  Connect-AzureRmAccount
  ```
2. 检查该帐户的订阅。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 如果有多个订阅，请选择要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. 接下来，使用以下 cmdlet 将 Azure 订阅添加到经典部署模型的 PowerShell。

  ```powershell
  Add-AzureAccount
  ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路
可以使用以下 cmdlet 将虚拟网络链接到 ExpressRoute 线路。 在运行 cmdlet 之前，请确保已创建虚拟网络网关并可将其用于进行链接。

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>删除指向线路的虚拟网络链接
可使用以下 cmdlet 删除指向 ExpressRoute 线路的虚拟网络链接。 请确保为给定的虚拟网络选择当前订阅。 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将另一订阅中的虚拟网络连接到线路
用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门可以使用自己的订阅部署其服务，但这些部门可以共享单个 ExpressRoute 线路以连接回本地网络。 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

> [!NOTE]
> 专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者。 所有虚拟网络共享相同的带宽。
> 
> 

![跨订阅连接](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>管理
“线路所有者”是在其中创建了 ExpressRoute 线路的订阅的管理员/共同管理员。 线路所有者可以授权其他订阅的管理员/共同管理员（称为线路用户）使用他们拥有的专用线路。 有权使用组织的 ExpressRoute 线路的线路用户在获得授权后可以将其订阅中的虚拟网络链接到 ExpressRoute 线路。

线路所有者有权随时修改和撤消授权。 撤消授权将导致从撤消其访问权限的订阅中删除所有链接。

### <a name="circuit-owner-operations"></a>线路所有者操作

“创建授权”

线路所有者可授权其他订阅的管理员使用指定的线路。 在下面的示例中，线路 (Contoso IT) 管理员允许另一个订阅（开发-测试）的管理员最多将两个虚拟网络链接到线路。 Contoso IT 管理员可以通过指定开发-测试 Microsoft ID 来启用此功能。 该 cmdlet 不会将电子邮件发送到指定的 Microsoft ID。 线路所有者需要显式通知其他订阅所有者：授权已完成。

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  返回：

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**查看授权**

线路所有者可以通过运行以下 cmdlet 来查看针对特定线路发出的所有授权：

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  返回：

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**更新授权**

线路所有者可以使用以下 cmdlet 修改授权：

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  返回：

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**删除授权**

线路所有者可以通过运行以下 cmdlet 来撤消/删除对用户的授权：

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>线路用户操作

**查看授权**

线路用户可以使用以下 cmdlet 查看授权：

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  返回：

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**兑现链接授权**

线路用户可以通过运行以下 cmdlet 来兑现链接授权：

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  返回：

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

在虚拟网络新链接的订阅中运行此命令：

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。