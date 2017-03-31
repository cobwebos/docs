---
title: "使用 Azure PowerShell 管理虚拟机 | Microsoft Docs"
description: "了解可用于在虚拟机管理过程中自动执行任务的命令。"
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 44235e32b7cf5b2be90f6d460ac22d7c0350f894
ms.lasthandoff: 03/27/2017


---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>使用 Azure PowerShell 管理虚拟机
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 有关使用 Resource Manager 模型的常见 PowerShell 命令，请参阅[此处](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

每天执行的许多管理 VM 的任务都可通过使用 Azure PowerShell cmdlet 自动执行。 本文提供较简单任务的示例命令，并提供演示更复杂任务的命令的文章链接。

> [!NOTE]
> 如果尚未安装和配置 Azure PowerShell，可在[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 一文中获取相关说明。
> 
> 

## <a name="how-to-use-the-example-commands"></a>如何使用示例命令
你需要将命令中的一些文本替换为适合你的环境的文本。 < 和 > 符号指示需要替换的文本。 替换文本时，请删除符号，但将引号保留在原处。

## <a name="get-a-vm"></a>获取 VM
这是你将经常使用的基本任务。 使用它可获取有关 VM 的信息、对 VM 执行任务或获取输出以存储在变量中。

若要获取有关 VM 的信息，请运行此命令，并替换引号内的所有内容（包括 < 和 > 字符）：

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

若要将输出存储在 $vm 变量中，请运行：

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>登录到基于 Windows 的 VM
运行以下命令：

> [!NOTE]
> 可以从显示的 **Get-AzureVM** 命令中获取虚拟机和云服务名称。
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<用于存储下载的 RDP 文件的驱动器和文件夹位置，示例：c:\temp>" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>停止 VM
运行以下命令：

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> 如果该 VM 是云服务中的最后一个 VM，则使用此参数可以保留该云服务的虚拟 IP (VIP)。 <br><br> 如果使用 StayProvisioned 参数，则仍要支付 VM 的费用。
> 
> 

## <a name="start-a-vm"></a>启动 VM
运行以下命令：

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>附加数据磁盘
此任务需要几个步骤。 首先，使用 ****Add-AzureDataDisk**** cmdlet 将磁盘添加到 $vm 对象。 然后，使用 **Update-AzureVM** cmdlet 更新 VM 的配置。

你还需要确定是要附加新的磁盘还是附加包含数据的磁盘。 对于新磁盘，此命令将创建并附加 .vhd 文件。

若要附加新磁盘，请运行以下命令：

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

若要附加现有数据磁盘，请运行以下命令：

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

若要从 blob 存储中现有的 .vhd 文件附加数据磁盘，请运行以下命令：

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>创建基于 Windows 的 VM
若要在 Azure 中创建新的基于 Windows 的虚拟机，请使用[使用 Azure PowerShell 创建和预配置基于 Windows 的虚拟机](create-powershell.md)中的说明。 本主题将介绍如何创建 Azure PowerShell 命令集，以创建可预先配置的基于 Windows 的 VM：

* Active Directory 域成员身份。
* 附加磁盘。
* 作为现有负载均衡集的成员。
* 静态 IP 地址。


