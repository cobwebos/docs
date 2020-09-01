---
title: 通过 Azure PowerShell 在 Azure Stack Edge 设备上部署 Vm
description: 介绍如何使用 Azure PowerShell 在 Azure Stack Edge 设备上创建和管理虚拟机 (Vm) 。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/11/2020
ms.author: alkohli
ms.openlocfilehash: 113f81c2c4209b851f2029bad2202fbb8bbfe103
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083066"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell-script"></a>通过 Azure PowerShell 脚本在 Azure Stack Edge 设备上部署 Vm

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教程介绍如何使用 Azure PowerShell 脚本在 Azure Stack Edge 设备上创建和管理 VM。

## <a name="prerequisites"></a>先决条件

使用此脚本开始在 Azure Stack Edge 设备上创建和管理 VM 之前，需要确保已完成以下步骤中列出的先决条件：

### <a name="for-azure-stack-edge-device-via-the-local-web-ui"></a>对于通过本地 web UI Azure Stack Edge 设备

1. 已根据 [步骤1：配置 Azure Stack Edge 设备](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-device)中所述完成了 Azure Stack 边缘设备上的网络设置。

2. 已为计算启用网络接口。 此网络接口 IP 用于为 VM 部署创建虚拟交换机。 以下步骤将引导你完成此过程：

    1. 请参阅 **计算设置**。 选择将用于创建虚拟交换机的网络接口。

        > [!IMPORTANT] 
        > 只能为计算配置一个端口。

    2. 在网络接口上启用计算。 Azure Stack 边缘创建并管理与该网络接口相对应的虚拟交换机。

3. 你已在你的 Azure Stack 边缘设备和客户端的受信任根存储中创建并安装了所有证书。 按照 [步骤2：创建和安装证书](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)中所述的过程进行操作。

### <a name="for-your-windows-client"></a>适用于你的 Windows 客户端

1. 在设备的本地 web UI 中，在 " **网络** " 页中定义了 Azure 一致性服务虚拟 internet 协议 (VIP) 。 需要将此 VIP 添加到：

    - 客户端上的主机文件，或
    - DNS 服务器配置
    
    > [!IMPORTANT]
    > 建议你修改用于终结点名称解析的 DNS 服务器配置。

    1. 以管理员身份启动 **记事本** (需要管理员权限才能) 保存文件，然后打开位于的 **主机** 文件 `C:\Windows\System32\Drivers\etc` 。
    
        ![Windows 资源管理器主机文件](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. 将以下条目添加到 **主机** 文件，并将替换为设备的适当值：
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        对于存储帐户，你可以提供希望脚本稍后用于创建新存储帐户的名称。 该脚本不会检查该存储帐户是否存在。

    3. 使用下图进行引用。 保存 **hosts** 文件。

        ![在记事本中承载文件](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. 下载此过程中使用[的 PowerShell 脚本](https://aka.ms/ase-vm-powershell)。

3. 确保你的 Windows 客户端运行的是 PowerShell 5.0 或更高版本。

4. 请确保 `Azure.Storage Module version 4.5.0` 系统上安装了。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0)获取此模块。 若要安装此模块，请键入：

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    若要验证已安装模块的版本，请键入：

    `Get-InstalledModule -name Azure.Storage`

    若要卸载任何其他版本模块，请键入：

    `Uninstall-Module -Name Azure.Storage`

5. 将[AzCopy 10 下载](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy)到你的 Windows 客户端。 记下该位置，因为在运行脚本时，会将其作为参数传递。

6. 确保 Windows 客户端运行的是 TLS 1.2 或更高版本。


## <a name="create-a-vm"></a>创建 VM

1. 以管理员身份运行 PowerShell。
2. 中转到你在客户端上下载该脚本的文件夹。  
3. 使用以下命令运行该脚本：
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    下面是运行脚本来创建 Windows VM 和 Linux VM 时的示例。

    **对于 Windows VM：**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **对于 Linux VM：**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. 若要清除该脚本创建的资源，请使用以下命令：
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>后续步骤

[使用 Azure PowerShell cmdlet 部署 Vm](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
