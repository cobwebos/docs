---
title: 教程：创建具有预留 IP 地址的自动缩放区域冗余应用程序网关 - Azure PowerShell
description: 在本教程中，了解如何使用 Azure PowerShell 创建具有预留 IP 地址的自动缩放区域冗余应用程序网关。
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99fa5d6f0ba74b56a53f2d1af1b99c7e5c2896a7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323194"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>教程：创建可改进 Web 应用程序访问的应用程序网关

如果你是希望改进 Web 应用程序访问的 IT 管理员，则可以优化应用程序网关，以根据客户需求进行缩放并跨多个可用性区域。 本教程可帮助你配置执行此操作的 Azure 应用程序网关功能：自动缩放、区域冗余和保留的 VIP（静态 IP）。 将使用 Azure PowerShell cmdlet 和 Azure 资源管理器部署模型来解决此问题。

> [!IMPORTANT] 
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建自动缩放虚拟网络
> * 创建保留的公共 IP
> * 设置应用程序网关基础结构
> * 指定自动缩放
> * 创建应用程序网关
> * 测试应用程序网关

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程要求在本地运行 Azure PowerShell。 必须安装 Azure PowerShell 模块 6.9.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 验证 PowerShell 版本以后，请运行 `Login-AzureRmAccount`，以便创建与 Azure 的连接。

## <a name="sign-in-to-azure"></a>登录 Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>创建资源组
在某个可用的位置创建资源组。

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

为自动缩放的应用程序网关创建一个包含一个专用子网的虚拟网络。 目前，在每个专用子网中，只能部署一个自动缩放的应用程序网关。

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>创建保留的公共 IP

将 PublicIPAddress 的分配方法指定为 Static。 自动缩放应用程序网关 VIP 只能为静态。 不支持动态 IP。 只支持标准 PublicIpAddress SKU。

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>检索详细信息

在本地对象中检索资源组、子网和 IP 的详细信息，以便创建应用程序网关的 IP 配置详细信息。

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>配置基础结构

使用与现有的标准应用程序网关相同的格式配置 IP 配置、前端 IP 配置、后端池、HTTP 设置、证书、端口、侦听器和规则。 新 SKU 与标准 SKU 遵循相同的对象模型。

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>指定自动缩放

现在可以为应用程序网关指定自动缩放配置。 支持两种自动缩放配置类型：

* **固定容量模式**。 在此模式下，应用程序网关不自动缩放，而是在固定缩放单元容量下运行。

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **自动缩放模式**。 在此模式下，应用程序网关根据应用程序流量模式自动缩放。

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>创建应用程序网关

创建应用程序网关，包括冗余区域和自动缩放配置。

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

使用 Get-AzureRmPublicIPAddress 获取应用程序网关的公共 IP 地址。 复制该公共 IP 地址或 DNS 名称，并将其粘贴到浏览器的地址栏。

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>清理资源

首先浏览使用应用程序网关创建的资源。 然后，如果不再需要资源组、应用程序网关和所有相关资源，可以使用 `Remove-AzureRmResourceGroup` 命令将其删除。

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用基于 URL 路径的路由规则创建应用程序网关](./tutorial-url-route-powershell.md)
