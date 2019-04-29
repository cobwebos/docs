---
title: 在 Azure 应用程序网关中重写 HTTP 标头
description: 本文介绍如何使用 Azure PowerShell 创建 Azure 应用程序网关和重写 HTTP 标头
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4747d824dcf531ed883d476a0daad182ea081c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715090"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>教程：创建应用程序网关和重写 HTTP 标头

可以使用 Azure PowerShell 在创建新的[自动缩放和区域冗余的应用程序网关 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 时配置[重写 HTTP 请求和响应标头的规则](rewrite-http-headers.md)。

> [!IMPORTANT] 
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 创建自动缩放虚拟网络
> * 创建保留的公共 IP
> * 设置应用程序网关基础结构
> * 指定 http 标头重写规则配置
> * 指定自动缩放
> * 创建应用程序网关
> * 测试应用程序网关

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

本教程要求在本地运行 Azure PowerShell。 必须安装 Az 模块 1.0.0 或更高版本。 依次运行 `Import-Module Az` 和 `Get-Module Az` 以查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。 验证 PowerShell 版本以后，请运行 `Login-AzAccount`，以便创建与 Azure 的连接。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>创建资源组
在某个可用的位置创建资源组。

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

为自动缩放的应用程序网关创建一个包含一个专用子网的虚拟网络。 目前，在每个专用子网中，只能部署一个自动缩放的应用程序网关。

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>创建保留的公共 IP

将 PublicIPAddress 的分配方法指定为 Static。 自动缩放应用程序网关 VIP 只能为静态。 不支持动态 IP。 只支持标准 PublicIpAddress SKU。

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>检索详细信息

在本地对象中检索资源组、子网和 IP 的详细信息，以便创建应用程序网关的 IP 配置详细信息。

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>配置基础结构

使用与现有的标准应用程序网关相同的格式配置 IP 配置、前端 IP 配置、后端池、HTTP 设置、证书、端口和侦听器。 新 SKU 与标准 SKU 遵循相同的对象模型。

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>指定 HTTP 标头重写规则配置

配置重写 http 标头所需的新对象：

- **RequestHeaderConfiguration**：此对象用于指定要重写的请求标头字段以及需要重写的原始标头的新值。
- **ResponseHeaderConfiguration**：此对象用于指定要重写的响应标头字段以及需要重写的原始标头的新值。
- **ActionSet**：此对象包含上面指定的请求和响应标头的配置。 
- **RewriteRule**：此对象包含上面指定的所有“actionSets”。 
- **RewriteRuleSet** - 此对象包含所有 rewriteRules，并且需要附加到（基本或基于路径的）请求路由规则。

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>指定路由规则

创建请求路由规则。 创建此重写配置后，会通过路由规则将其附加到源侦听器。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 因此，它仅适用于站点的特定路径区域。 下面创建了一个基本的路由规则并附加了重写规则集。

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>指定自动缩放

现在可以为应用程序网关指定自动缩放配置。 支持两种自动缩放配置类型：

* **固定容量模式**。 在此模式下，应用程序网关不自动缩放，而是在固定缩放单元容量下运行。

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **自动缩放模式**。 在此模式下，应用程序网关根据应用程序流量模式自动缩放。

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>创建应用程序网关

创建应用程序网关，包括冗余区域和自动缩放配置。

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

使用 Get-AzPublicIPAddress 获取应用程序网关的公共 IP 地址。 复制该公共 IP 地址或 DNS 名称，并将其粘贴到浏览器的地址栏。

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>清理资源

首先浏览使用应用程序网关创建的资源。 然后，如果不再需要资源组、应用程序网关和所有相关资源，可以使用 `Remove-AzResourceGroup` 命令将其删除。

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用基于 URL 路径的路由规则创建应用程序网关](./tutorial-url-route-powershell.md)
