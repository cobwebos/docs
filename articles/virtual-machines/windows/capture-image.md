---
title: "从通用 Azure VM 捕获 VM 映像 | Microsoft Docs"
description: "了解如何从 Resource Manager 部署模型中创建的通用 Azure VM 捕获 VM 映像"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 8f1d488fd8f71bf90c8bf7b7c1544445ffbd7686
ms.contentlocale: zh-cn
ms.lasthandoff: 05/27/2017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>如何从通用 Azure VM 捕获 VM 映像
本文说明如何使用 Azure PowerShell 创建通用 Azure VM 的映像。 然后可以使用该映像来创建另一个 VM。 该映像包含 OS 磁盘和附加到虚拟机的数据磁盘。 该映像不包含虚拟网络资源，因此，创建新 VM 时需要设置这些资源。 

## <a name="prerequisites"></a>先决条件
需要安装 Azure PowerShell 1.0.x 版或更新版本。 如果尚未安装 PowerShell，请参阅 [How to install and configure Azure PowerShell](/powershell/azure/overview)（如何安装和配置 Azure PowerShell）了解安装步骤。

## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 通用化 Windows VM

Sysprep 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](http://technet.microsoft.com/library/bb457073.aspx)。

确保 Sysprep 支持计算机上运行的服务器角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果在首次将 VHD 上传到 Azure 之前运行 Sysprep，请确保先[准备好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，然后再运行 Sysprep。 
> 
> 

1. 登录到 Windows 虚拟机。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 **%windir%\system32\sysprep**，然后运行 `sysprep.exe`。
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。
4. 在“关机选项”中选择“关机”。
5. 单击 **“确定”**。
   
    ![启动 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 在完成运行后会关闭虚拟机。 请勿重启 VM。


还可以使用 `sudo waagent -deprovision+user` 通用化 Linux VM，然后使用 PowerShell 捕获该 VM。 有关使用 CLI 捕获 VM 的信息，请参阅[如何使用 Azure CLI 对 Linux 虚拟机进行通用化和捕获](../linux/capture-image.md)

## <a name="log-in-to-azure-powershell"></a>登录到 Azure PowerShell
1. 打开 Azure PowerShell 并登录到 Azure 帐户。
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    此时将打开一个弹出窗口让输入 Azure 帐户凭据。
2. 获取可用订阅的订阅 ID。
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. 使用订阅 ID 设置正确的订阅。
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>解除分配 VM 并将状态设置为通用化
1. 解除分配 VM 资源。
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure 门户中该 VM 的“状态”将从“已停止”更改为“已停止(已解除分配)”。
2. 将虚拟机的状态设置为“通用化”。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. 检查 VM 的状态。 VM 的“OSState/通用化”部分中的“DisplayStatus”应设置为“VM 已通用化”。  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>创建映像
1. 使用以下命令将虚拟机映像复制到目标存储容器。 该映像在创建时所在的存储帐户与原始虚拟机的相同。 `-Path` 参数在本地保存 JSON 模板的副本。 `-DestinationContainerName` 参数是要在其中保存映像的容器的名称。 如果该容器不存在，系统将自动创建。
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    可以从 JSON 文件模板获取映像的 URL。 转到“资源” > “storageProfile” > “osDisk” > “映像” > “URI”部分即可查找映像的完整路径。 映像的 URL 如下所示：`https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`。
   
    也可以在门户中验证 URI。 映像将复制到存储帐户中名为 **system** 的容器中。 

## <a name="next-steps"></a>后续步骤
* 现在，可以[从映像创建 VM](create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


