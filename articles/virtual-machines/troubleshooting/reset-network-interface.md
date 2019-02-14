---
title: 如何为 AzureWindows VM 重置网络接口 | Microsoft Docs
description: 介绍如何为 Azure Windows VM 重置网络接口
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982873"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>如何为 Azure Windows VM 重置网络接口 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

本文介绍如何重置 Azure Windows VM 的网络接口，以便在执行以下操作后无法连接到 Microsoft Azure Windows 虚拟机 (VM) 时解决问题：

* 禁用默认网络接口 (NIC)。 
* 为 NIC 手动设置静态 IP。 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>重置网络接口

### <a name="for-vms-deployed-in-resource-group-model"></a>对于部署在资源组模型中的 VM

1.  转到 [Azure 门户](https://ms.portal.azure.com)。
2.  选择受影响的虚拟机。
3.  选择“网络”，然后选择 VM 的网络接口。

    ![网络接口位置](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  选择“IP 配置”。
5.  选择该 IP。 
6.  如果“专用 IP 分配”不是“静态”，则将其更改为“静态”。
7.  将“IP 地址”更改为子网中可用的其他 IP 地址。
8. 虚拟机将重新启动以将新的 NIC 初始化到系统。
9.  尝试通过远程桌面协议连接到计算机。 如果成功，可以根据需要将专用 IP 地址更改回原始 IP 地址。 否则，可以保留它。 

#### <a name="use-azure-powershell"></a>使用 Azure PowerShell

1. 确保[已安装最新的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
2. 打开提升的 Azure PowerShell 会话（以管理员身份运行）。 运行以下命令：

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. 尝试通过远程桌面协议连接到计算机。  如果成功，可以根据需要将专用 IP 地址更改回原始 IP 地址。 否则，可以保留它。

### <a name="for-classic-vms"></a>对于经典 VM

要重置网络接口，请执行下列步骤：

#### <a name="use-azure-portal"></a>使用 Azure 门户

1.  转到 [Azure 门户]( https://ms.portal.azure.com)。
2.  选择“虚拟机(经典)”。
3.  选择受影响的虚拟机。
4.  选择“IP 地址”。
5.  如果“专用 IP 分配”不是“静态”，则将其更改为“静态”。
6.  将“IP 地址”更改为子网中可用的其他 IP 地址。
7.  选择“保存”。
8.  虚拟机将重新启动以将新的 NIC 初始化到系统。
9.  尝试通过远程桌面协议连接到计算机。 如果成功，可以选择将专用 IP 地址重新还原为原始 IP 地址。  

#### <a name="use-azure-powershell"></a>使用 Azure PowerShell

1. 确保[已安装最新的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)。
2. 打开提升的 Azure PowerShell 会话（以管理员身份运行）。 运行以下命令：

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. 尝试通过远程桌面协议连接到计算机。 如果成功，可以根据需要将专用 IP 地址更改回原始 IP 地址。 否则，可以保留它。 

## <a name="delete-the-unavailable-nics"></a>删除不可用的 NIC
通过远程桌面连接到计算机后，必须删除旧的 NIC 以避免出现潜在问题：

1.  打开“设备管理器”。
2.  选择“视图” > “显示隐藏的设备”。
3.  选择“网络适配器”。 
4.  找到名为“Microsoft Hyper-V 网络适配器”的适配器。
5.  你会看到灰显的不可用适配器。右键单击该适配器，然后选择“卸载”。

    ![NIC 图像](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > 仅卸载名称为“Microsoft Hyper-V 网络适配器”的不可用适配器。 如果卸载任何其他隐藏的适配器，可能会导致其他问题。
    >
    >

6.  现在，所有不可用的适配器应该都已从系统中清除。
