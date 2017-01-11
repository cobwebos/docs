---
title: "VM 的常用 PowerShell 命令 | Microsoft Docs"
description: "可用于在 Windows 设备上的 Azure 中创建和管理 VM 的常用 PowerShell 命令"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: d86db0f0487c8c3627fa91b656db6a4c1576348c


---
# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>创建和管理 VM 的常用 PowerShell 命令
本文介绍一些可用于在 Azure 订阅中创建和管理虚拟机的 Azure PowerShell 命令。  若要获取特定命令行开关和选项的详细帮助，可以使用 **Get-Help** *命令*。

有关安装最新版 Azure PowerShell、选择订阅和登录到帐户的信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

## <a name="create-a-vm"></a>创建 VM
| 任务 | 命令 |
| --- | --- |
| 创建 VM 配置 |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm_name" -VMSize "vm_size"<BR></BR><BR></BR>VM 配置用于定义或更新 VM 的设置。 使用 VM 的名称及其[大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)对配置进行初始化。 |
| 添加配置设置 |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>包括[凭据](https://technet.microsoft.com/library/hh849815.aspx)的操作系统设置将添加到以前使用 New-AzureRmVMConfig 创建的配置对象。 |
| 添加网络接口 |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM 必须使用[网络接口](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)在虚拟网络中通信。 还可使用 [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) 检索现有网络接口对象。 |
| 指定平台映像 |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[映像信息](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)将添加到以前使用 New-AzureRmVMConfig 创建的配置对象。 仅当将操作系统磁盘设置为使用平台映像时，才使用此命令返回的对象。 |
| 设置要使用平台映像的操作系统磁盘 |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>操作系统磁盘的名称以及它在[存储](../storage/storage-powershell-guide-full.md)中的位置将添加到以前创建的配置对象。 |
| 设置要使用一般化映像的操作系统磁盘 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>操作系统磁盘的名称、源映像的位置，以及磁盘在[存储](../storage/storage-powershell-guide-full.md)中的位置将添加到以前创建的配置对象。 |
| 设置要使用特殊化映像的操作系统磁盘 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "name_of_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| 创建 VM |[New-AzureRmVM]() -ResourceGroupName "resource_group_name" -Location "location_name" -VM $vm<BR></BR><BR></BR>所有资源在[资源组](../powershell-azure-resource-manager.md)中创建。 必须在资源组所在的同一[位置](https://msdn.microsoft.com/library/azure/dn495177.aspx)创建 VM。 运行此命令之前，请运行 New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface 和 Set-AzureRmVMOSDisk。 |

## <a name="get-information-about-vms"></a>获取有关 VM 的信息
| 任务 | 命令 |
| --- | --- |
| 列出订阅中的 VM |[Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| 列出资源组中的 VM |Get-AzureRmVM -ResourceGroupName "resource_group_name"<BR></BR><BR></BR>若要获取订阅中的资源组列表，请使用 [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx)。 |
| 获取有关 VM 的信息 |Get-AzureRmVM -ResourceGroupName "resource_group_name" -Name "vm_name" |

## <a name="manage-vms"></a>管理 VM
| 任务 | 命令 |
| --- | --- |
| 启动 VM |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 停止 VM |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 重启正在运行的 VM |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 删除 VM |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| 对 VM 进行一般化 |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm_name" -Generalized<BR></BR><BR></BR>在运行 Save-AzureRmVMImage 之前运行此命令。 |
| 捕获 VM |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource_group_name" -VMName "vm_name" -DestinationContainerName "image_container" -VHDNamePrefix "image_name_prefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>虚拟机必须[关闭并通用化](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)才能用于创建映像。 运行此命令之前，请运行 Set-AzureRmVm。 |
| 更新 VM |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource_group_name" -VM $vm<BR></BR><BR></BR>使用 Get-AzureRmVM 获取当前 VM 配置，更改 VM 对象上的配置设置，然后运行此命令。 |
| 将数据磁盘添加到 VM |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>使用 Get-AzureRmVM 获取 VM 对象。 指定 LUN 号和磁盘大小。 运行 Update-AzureRmVM 将配置更改应用到 VM。 添加的磁盘未进行初始化。 有关添加磁盘时初始化磁盘的信息，请参阅[使用 Resource Manager 与 PowerShell 来管理 Azure 虚拟机](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 |
| 从 VM 中删除数据磁盘 |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk_name"<BR></BR><BR></BR>使用 Get-AzureRmVM 获取 VM 对象。 运行 Update-AzureRmVM 将配置更改应用到 VM。 |
| 将扩展添加到 VM |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource_group_name" -Location "azure_location" -VMName "vm_name" -Name "extension_name" -Publisher "publisher_name" -Type "extension_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>使用要安装的扩展的相应[配置信息](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)运行此命令。 |
| 删除 VM 扩展 |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource_group_name" -Name "extension_name" -VMName "vm_name" |

## <a name="next-steps"></a>后续步骤
* 请参阅[使用 Resource Manager 和 PowerShell 创建 Windows VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 中有关创建虚拟机的基本步骤。




<!--HONumber=Dec16_HO2-->


