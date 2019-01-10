---
title: 使用 Azure 自定义路由通过强制隧道启用 KMS 激活 | Microsoft Docs
description: 介绍如何在 Azure 中使用 Azure 自定义路由通过强制隧道启用 KMS 激活。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797333"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>在强制隧道方案中，Windows 激活失败

本文介绍如何在站点到站点 VPN 连接或 ExpressRoute 方案中启用了强制隧道时，解决可能遇到的 KMS 激活问题。

## <a name="symptom"></a>症状

在 Azure 虚拟网络子网上启用[强制隧道](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)，以将所有绑定 Internet 的流量重新定向到本地网络。 在此方案中，运行 Windows Server 2012 R2 或更高版本的 Azure 虚拟机 (VM) 可成功激活 Windows。 然而，运行 Windows 以前版本的 VM 无法激活 Windows。 

## <a name="cause"></a>原因

Azure Windows VM 需要连接到 Azure KMS 服务器才能激活 Windows。 激活要求激活请求必须来自 Azure 公共 IP 地址。 在强制隧道方案中，激活将失败，因为激活请求来自本地网络而不是来自 Azure 公共 IP。 

## <a name="solution"></a>解决方案

若要解决此问题，请使用 Azure 自定义路由，将激活流量路由到 Azure KMS 服务器 (23.102.135.246)。 

IP 地址 23.102.135.246 是 Azure 全球云的 KMS 服务器的 IP 地址。 其 DNS 名称是 kms.core.windows.net。 如果使用其他 Azure 平台（如 Azure 德国），则必须使用相应 KMS 服务器的 IP 地址。 有关详细信息，请参阅下表：

|平台| KMS DNS|KMS IP|
|------|-------|-------|
|Azure 全球|kms.core.windows.net|23.102.135.246|
|Azure 德国|kms.core.cloudapi.de|51.4.143.248|
|Azure 美国政府版|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure 中国世纪互联|kms.core.chinacloudapi.cn|42.159.7.249|


若要添加自定义路由，请执行以下步骤：

### <a name="for-resource-manager-vms"></a>对于资源管理器 VM

1. 打开 Azure PowerShell，然后[登录到 Azure 订阅](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
2. 运行以下命令：

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. 转到存在激活问题的 VM，使用 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) 测试它是否可以访问 KMS 服务器：

        psping kms.core.windows.net:1688

4. 尝试激活 Windows 并查看问题是否得以解决。

### <a name="for-classic-vms"></a>对于经典 VM

1. 打开 Azure PowerShell，然后[登录到 Azure 订阅](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。
2. 运行以下命令：

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. 转到存在激活问题的 VM，使用 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) 测试它是否可以访问 KMS 服务器：

        psping kms.core.windows.net:1688

4. 尝试激活 Windows 并查看问题是否得以解决。

## <a name="next-steps"></a>后续步骤

- [KMS 客户端安装密钥](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [查看并选择激活方法](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)