---
title: 使用 Azure PowerShell 部署和配置 Azure 防火墙
description: 本文介绍如何使用 Azure PowerShell 部署和配置 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 494beb6ba2bf8a9409962b4418089cdad0e182e1
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114783"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>使用 Azure PowerShell 部署和配置 Azure 防火墙

控制出站网络访问是整个网络安全计划的重要组成部分。 例如，你可能想要限制对网站的访问， 或者限制可以访问的出站 IP 地址和端口。

可以控制 Azure 子网的出站网络访问的一种方法是使用 Azure 防火墙。 使用 Azure 防火墙，可以配置：

* 应用程序规则，用于定义可从子网访问的完全限定域名 (FQDN)。
* 网络规则，用于定义源地址、协议、目标端口和目标地址。

将网络流量路由到用作子网默认网关的防火墙时，网络流量受到配置的防火墙规则的控制。

在本文中，你将创建一个包含三个子网的简化 VNet，以便于部署。 对于生产部署，我们建议使用[中心辐射模型](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)，其中，防火墙在其自身的 VNet 中。 工作负荷服务器在包含一个或多个子网的同一区域中的对等 VNet 内。

* **AzureFirewallSubnet** - 防火墙在此子网中。
* **Workload-SN** - 工作负荷服务器在此子网中。 此子网的网络流量通过防火墙。
* **Jump-SN** -“跳转”服务器在此子网中。 可以使用远程桌面连接到跳转服务器中的公共 IP 地址。 然后，可在跳转服务器中（使用另一个远程桌面）连接到工作负荷服务器。

![教程网络基础结构](media/tutorial-firewall-rules-portal/Tutorial_network.png)

在本文中，学习如何：

> [!div class="checklist"]
> * 设置测试网络环境
> * 部署防火墙
> * 创建默认路由
> * 配置一个应用程序规则以允许访问 www.google.com
> * 配置网络规则，以允许访问外部 DNS 服务器
> * 测试防火墙

如果需要，可以使用 [Azure 门户](tutorial-firewall-deploy-portal.md)完成此过程。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

此过程要求在本地运行 PowerShell。 必须安装 Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps)。 验证 PowerShell 版本以后，请运行 `Connect-AzAccount`，以便创建与 Azure 的连接。

## <a name="set-up-the-network"></a>设置网络

首先，创建一个资源组用于包含部署防火墙所需的资源。 然后创建 VNet、子网和测试服务器。

### <a name="create-a-resource-group"></a>创建资源组

资源组包含用于部署的所有资源。

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>创建 VNet

此虚拟网络有三个子网：

> [!NOTE]
> AzureFirewallSubnet 子网的大小为/26。 有关子网大小的详细信息, 请参阅[Azure 防火墙常见问题解答](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```
现在，创建虚拟网络：

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>创建虚拟机

现在，创建跳转虚拟机和工作负荷虚拟机，并将其放入相应的子网。
出现提示时，请键入该虚拟机的用户名和密码。

创建 Srv-Jump 虚拟机。

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

创建没有公共 IP 地址的工作负荷虚拟机。
出现提示时，请键入该虚拟机的用户名和密码。

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>部署防火墙

现在，将防火墙部署到虚拟网络。

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

记下专用 IP 地址。 稍后在创建默认路由时需要用到此地址。

## <a name="create-a-default-route"></a>创建默认路由

创建一个禁用 BGP 路由传播的表

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>配置应用程序规则

此应用程序规则允许对 www.google.com 进行出站访问。

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Azure 防火墙包含默认情况下允许的基础结构 FQDN 的内置规则集合。 这些 FQDN 特定于平台，不能用于其他目的。 有关详细信息，请参阅[基础结构 FQDN](infrastructure-fqdns.md)。

## <a name="configure-a-network-rule"></a>配置网络规则

此网络规则允许对端口 53 (DNS) 上的两个 IP 地址进行出站访问。

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>更改 **Srv-Work** 网络接口的主要和辅助 DNS 地址

为了在此过程中进行测试，请配置服务器的主要和辅助 DNS 地址。 这并不是一项常规的 Azure 防火墙要求。

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>测试防火墙

现在测试防火墙，以确认它是否按预期方式工作。

1. 记下 **Srv-Work** 虚拟机的专用 IP 地址：

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. 将远程桌面连接到 **Srv-Jump** 虚拟机，然后登录。 在该虚拟机中，与 **Srv-Work** 专用 IP 地址建立远程桌面连接并登录。

3. 在 **SRV-Work** 上，打开 PowerShell 窗口并运行以下命令：

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   这两个命令都应返回应答，表明 DNS 查询正在通过防火墙。

1. 运行以下命令：

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   www.google.com 请求应成功，而 www.microsoft.com 请求应失败。 这表明防火墙规则按预期运行。

现已验证防火墙规则可正常工作：

* 可以使用配置的外部 DNS 服务器解析 DNS 名称。
* 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。

## <a name="clean-up-resources"></a>清理资源

可以将防火墙资源保留到下一教程使用。不再需要时，请删除 **Test-FW-RG** 资源组，以删除与防火墙相关的所有资源：

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>后续步骤

* [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
