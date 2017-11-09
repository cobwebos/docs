---
title: "将云服务连接到自定义域控制器 | Microsoft Docs"
description: "了解如何使用 PowerShell 和 AD 域扩展将 Web/辅助角色连接到自定义 AD 域"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 17f6918371678ac849198bff4e3b3eea8678c660
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>将 Azure 云服务角色连接到 Azure 中托管的自定义 AD 域控制器
我们先在 Azure 中设置一个虚拟网络 (VNet)。 然后将 Active Directory 域控制器（托管在 Azure 虚拟机上）添加到该 VNet。 接下来，将现有云服务角色添加预先创建的 VNet，然后将它们连接到域控制器。

在开始之前，请特别注意以下几点：

1. 本教程使用 PowerShell，因此请确保 Azure PowerShell 已安装并已准备就绪。 有关设置 Azure PowerShell 的帮助，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
2. AD 域控制器和 Web/辅助角色实例需要在 VNet 中。

请遵循以下分步指南，如果你遇到任何问题，请在本文末尾留言。 我们将回复你（没错，我们真的会阅读留言）。

由云服务引用的网络必须为**经典虚拟网络**。

## <a name="create-a-virtual-network"></a>创建虚拟网络
可以使用 Azure 门户或 PowerShell 在 Azure 中创建虚拟网络。 在本教程中，我们将使用 PowerShell。 若要使用 Azure 门户创建虚拟网络，请参阅[创建虚拟网络](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)。

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>创建虚拟机
完成虚拟网络的设置后，需要创建 AD 域控制器。 在本教程中，我们会在 Azure 虚拟机上设置 AD 域控制器。

为此，请使用以下命令通过 PowerShell 创建虚拟机：

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>将虚拟机提升为域控制器
要将虚拟机配置为 AD 域控制器，需要登录 VM 并对其进行配置。

若要登录 VM，你可以通过 PowerShell 获取 RDP 文件；请使用以下命令：

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

登录 VM 后，请根据[如何设置客户 AD 域控制器](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)中的分步指导，将虚拟机设置为 AD 域控制器。

## <a name="add-your-cloud-service-to-the-virtual-network"></a>将云服务添加到虚拟网络
接下来，需要将云服务部署添加到新的 VNet。 为此，请使用 Visual Studio 或选择的编辑器将相关节添加到 cscfg，以修改云服务 cscfg。

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

接下来，请生成云服务项目并将它部署到 Azure。 有关将云服务包部署到 Azure 的帮助，请参阅[如何创建和部署云服务](cloud-services-how-to-create-deploy.md#how-to-deploy-a-cloud-service)

## <a name="connect-your-webworker-roles-to-the-domain"></a>将 Web/辅助角色连接到域
在 Azure 上部署云服务项目后，请使用 AD 域扩展将角色实例连接到自定义 AD 域。 要将 AD 域扩展添加到现有云服务部署并加入自定义域，请在 PowerShell 中执行以下命令：

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

这就是所有的操作。

云服务应已加入自定义域控制器。 如果你想要深入了解用于配置 AD 域扩展的其他选项，请使用 PowerShell 帮助。 下面是一些示例：

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
