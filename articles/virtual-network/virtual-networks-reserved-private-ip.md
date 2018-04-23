---
title: 静态内部专用 IP - Azure VM - 经典
description: 了解静态内部 IP (DIP) 以及如何对其进行管理
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.openlocfilehash: b668a06b91a5fcb1dd08737e0422b599bdb3e27f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>如何使用 PowerShell（经典）设置静态内部专用 IP
大多数情况下，不需要指定虚拟机的静态内部 IP 地址。 虚拟网络中的 VM 会自动从指定的范围接收内部 IP 地址。 但在某些情况下，需要为特定 VM 指定静态 IP 地址。 例如，在 VM 需要运行 DNS 或将要成为域控制器的情况下。 静态内部 IP 地址会始终与 VM 关联在一起，即使经历“停止/取消预配”状态变化。 

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用[Resource Manager 部署模型](virtual-networks-static-private-ip-arm-ps.md)。
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>如何验证特定 IP 地址是否可用：
若要验证 IP 地址 *10.0.0.7* 在名为 *TestVNet* 的 VNet 中是否可用，请运行以下 PowerShell 命令并验证 *IsAvailable* 的值：

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> 若要在安全环境中测试上面的命令，请遵循[创建虚拟网络（经典）](virtual-networks-create-vnet-classic-pportal.md)中的指南，创建名为“TestVnet”的 VNet，并确保它使用“10.0.0.0/8”地址空间。
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>如何在创建 VM 时指定静态内部 IP
下面的 PowerShell 脚本将创建名为 *TestService* 的全新云服务，然后从 Azure 中检索映像，接着在新的云服务中使用检索的映像创建名为 *TestVM* 的 VM，对该 VM 进行设置，使之位于名为 *Subnet-1* 的子网中，最后再将 *10.0.0.7* 设置为 VM 的静态内部 IP：

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>如何检索 VM 的静态内部 IP 信息
要查看使用上述脚本创建的 VM 的静态内部 IP 信息，请运行以下 PowerShell 命令，并观察 *IpAddress* 的值：

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>如何从 VM 中删除静态内部 IP
若要删除在上述脚本中添加到 VM 中的静态内部 IP，请运行以下 PowerShell 命令：

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>如何向现有 VM 添加静态内部 IP
若要向使用以上脚本创建的 VM 添加静态内部 IP，请运行以下命令：

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>后续步骤
[保留 IP](virtual-networks-reserved-public-ip.md)

[实例层级公共 IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)

