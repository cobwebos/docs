---
title: "创建 Azure 虚拟机规模集 | Microsoft Docs"
description: "使用 Azure CLI、PowerShell、模板或 Visual Studio 创建和部署 Linux 或 Windows Azure 虚拟机规模集。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.openlocfilehash: 32af01aa545c541688128a7ae6bbb82a0e046f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>创建和部署虚拟机规模集
使用虚拟机规模集可以轻松地将相同的虚拟机作为集来进行部署和管理。 规模集为超大规模应用程序提供高度可缩放且可自定义的计算层，并且它们支持 Windows 平台映像、Linux 平台映像、自定义映像和扩展。 有关规模集的详细信息，请参阅[虚拟机规模集](virtual-machine-scale-sets-overview.md)。

本教程介绍如何**不**使用 Azure 门户就创建虚拟机规模集。 有关如何使用 Azure 门户的信息，请参阅[如何使用 Azure 门户创建虚拟机规模集](virtual-machine-scale-sets-portal-create.md)。

>[!NOTE]
>有关 Azure 资源管理器资源的详细信息，请参阅 [Azure 资源管理器与经典部署](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

如果要使用 Azure CLI 2.0 或 Azure PowerShell 创建规模集，首先需要登录到订阅。

有关如何使用 Azure CLI 或 PowerShell 安装、设置和登录到 Azure 的详细信息，请参阅 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli.md)或 [Azure PowerShell cmdlet 入门](/powershell/azure/overview)。

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>创建资源组

首先需要创建虚拟机规模集所关联的资源组。

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

## <a name="create-from-azure-cli"></a>从 Azure CLI 创建

使用 Azure CLI，只需最少的工作量就可创建虚拟机规模集。 如果省略默认值，则将提供它们。 例如，如果未指定任何虚拟网络信息，将创建一个虚拟网络。 如果省略以下部分，则会创建它们： 
- 负载均衡器
- 虚拟网络
- 公共 IP 地址

选择要用于虚拟机规模集的虚拟机映像时，有几个选择：

- URN  
资源的标识符：  
**Win2012R2Datacenter**

- URN 别名  
URN 的友好名称：  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- 自定义资源 ID  
Azure 资源的路径：  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

- Web 资源  
HTTP URI 的路径：  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**

>[!TIP]
>使用 `az vm image list` 可获取可用映像的列表。

若要创建虚拟机规模集，必须指定以下各项：

- 资源组 
- Name
- 操作系统映像
- 身份验证信息 
 
以下示例创建基本虚拟机规模集（此步骤可能需要几分钟）。

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

该命令完成后，虚拟机规模集就创建好了。 可能需要获取虚拟机的 IP 地址，以便可以连接到该虚拟机。 可以使用以下命令获取有关虚拟机的大量不同信息（包括 IP 地址）。 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>从 PowerShell 创建

PowerShell 的使用比 Azure CLI 更复杂。 Azure CLI 为网络相关资源（如负载均衡器、IP 地址和虚拟网络）提供默认值，而 PowerShell 则不提供。 使用 PowerShell 引用映像也稍微复杂一些。 可以使用以下 cmdlet 获取映像：

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

cmdlet 的运行结果可以按顺序通过管道传送。 下面是如何获取其发布服务器名称为 **microsoft** 的“美国西部 2”区域的所有映像的一个示例。

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

用于创建虚拟机规模集的工作流如下所示：

1. 创建包含规模集相关信息的配置对象。
2. 引用基本 OS 映像。
3. 配置操作系统设置：身份验证、VM 名称前缀及用户/密码。
4. 配置网络。
5. 创建规模集。

此示例为安装了 Windows Server 2016 的计算机创建基本 2 实例规模集。

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

### <a name="using-a-custom-virtual-machine-image"></a>使用自定义虚拟机映像
如果要从自己的自定义映像创建规模集，而不是从库中引用虚拟机映像，Set-AzureRmVmssStorageProfile 命令将如下所示：
```PowerShell
Set-AzureRmVmssStorageProfile -OsDiskCreateOption FromImage -ManagedDisk PremiumLRS -OsDiskCaching "None" -OsDiskOsType Linux -ImageReferenceId (Get-AzureRmImage -ImageName $VMImage -ResourceGroupName $rg).id
```

## <a name="create-from-a-template"></a>从模板创建

可使用 Azure 资源管理器模板部署虚拟机规模集。 可以创建自己的模板，也可以使用[模板存储库](https://azure.microsoft.com/resources/templates/?term=vmss)中的模板。 可以直接将这些模板部署到 Azure 订阅。

>[!NOTE]
>若要创建自己的模板，请创建 JSON 文本文件。 有关如何创建和自定义模板的常规信息，请参阅 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。

[GitHub 上](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set)提供了一个示例模板。 有关如何创建和使用该示例的详细信息，请参阅[最小可行规模集](.\virtual-machine-scale-sets-mvss-start.md)。

## <a name="create-from-visual-studio"></a>从 Visual Studio 创建

使用 Visual Studio，可以创建 Azure 资源组项目，并向其添加虚拟机规模集模板。 可以选择是从 GitHub 还是从 Azure Web 应用程序库将其导入。 还会生成部署 PowerShell 脚本。 有关详细信息，请参阅[如何使用 Visual Studio 创建虚拟机规模集](virtual-machine-scale-sets-vs-create.md)。

## <a name="create-from-the-azure-portal"></a>从 Azure 门户中创建

Azure 门户提供了快速创建规模集的简便方式。 有关详细信息，请参阅[如何使用 Azure 门户创建虚拟机规模集](virtual-machine-scale-sets-portal-create.md)。

## <a name="next-steps"></a>后续步骤

详细了解[数据磁盘](virtual-machine-scale-sets-attached-disks.md)。

了解如何[管理应用](virtual-machine-scale-sets-deploy-app.md)。
