---
title: "管理 Azure 堆栈中的 VM 磁盘 |Microsoft 文档"
description: "Azure 堆栈的虚拟机的设置磁盘。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Azure 堆栈的虚拟机磁盘存储

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈支持使用[非托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)作为操作系统 (OS) 磁盘和数据磁盘的虚拟机中。 若要使用非托管的磁盘，你可以创建[存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)，然后将磁盘存储容器中的存储帐户中作为页 blob。 然后，这些磁盘称为 VM 磁盘。

若要提高性能并降低 Azure 堆栈系统的管理成本，我们建议将每个 VM 磁盘放置在单独的容器中。 容器应包含与 OS 磁盘或数据磁盘，但不是能同时在同一时间。 但是，可防止将放到同一个容器都没有限制。

如果你将一个或多个数据磁盘添加到 VM 时，计划使用其他容器作为一个位置来保存这些磁盘。 数据磁盘，如更多虚拟机的 OS 磁盘还应在其自己单独的容器中。

当你创建多个 Vm 时，你可以为每个新的 VM 来重用相同的存储帐户。 创建容器应是唯一的。  

若要将磁盘添加到 VM，使用用户门户或 PowerShell。

| 方法 | 选项
|-|-|
|[用户门户](#use-the-portal-to-add-additional-disks-to-a-vm)|-将新数据磁盘添加到以前设置的 VM。 新的磁盘都是由 Azure 堆栈创建的。 </br> </br>-将现有的.vhd 文件作为磁盘添加到以前设置的 VM。 若要执行此操作首先必须准备并将.vhd 文件上载到 Azure 堆栈。 |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -创建新的 VM 与 OS 磁盘，并在同一时间将一个或多个数据磁盘添加到该 VM。 |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>使用门户将更多的磁盘添加到 VM
默认情况下，当你使用门户为大多数应用商店项，创建 VM 时被创建仅 OS 磁盘。 创建 azure 磁盘称为被管理的磁盘。

预配 VM 后，你可以使用门户将新的数据磁盘或现有的数据磁盘添加到该 VM。 每个附加磁盘都应置于单独的容器。 你将添加到 VM 的磁盘称为非托管的磁盘。

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>使用门户将新的数据磁盘附加到 VM

1.  在门户中，单击**虚拟机**。    
    ![示例： 虚拟机仪表板](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  选择以前设置的虚拟机。   
    ![示例： 在仪表板中选择一个 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  对于虚拟机中，单击**磁盘** > **附加新**。       
    ![示例： 将新磁盘附加到 vm](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  在**附加新磁盘**窗格中，单击**位置**。 默认情况下，位置设置为包含操作系统磁盘的同一容器。      
    ![示例： 设置的磁盘位置](media/azure-stack-manage-vm-disks/disk-location.png)

5.  选择**存储帐户**使用。 接下来，选择**容器**你想要将数据磁盘。 从**容器**页上，你可以创建一个新的容器如果你想。 然后，你可以将新磁盘的位置更改为其自己的容器。 当每个磁盘使用单独的容器时，你将分发可以提高性能的数据磁盘的位置。 单击**选择**以保存所选内容。     
    ![示例： 选择容器](media/azure-stack-manage-vm-disks/select-container.png)

6.  在**附加新磁盘**页上，更新**名称**，**类型**，**大小**，和**主机缓存**设置磁盘。 然后单击**确定**保存新 vm 的磁盘配置。  
    ![示例： 完整的磁盘附件](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  新磁盘 Azure 堆栈将创建磁盘，并将其附加到虚拟机后，被列入下的虚拟机的磁盘设置**数据磁盘**。   
    ![示例： 查看磁盘](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>将现有数据磁盘附加到 VM
1.  [准备.vhd 文件](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd)以用作 VM 数据磁盘。 将该.vhd 文件上载到你想要附加到的.vhd 文件在 vm 使用的存储帐户。

  若要使用不同的容器保存.vhd 文件中包含操作系统磁盘的容器的计划。   
  ![示例： 上载 VHD 文件](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  上载的.vhd 文件后，你就可以将 VHD 附加到 VM。 在左侧菜单中，单击**虚拟机**。  
 ![示例： 在仪表板中选择一个 VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  从列表中选择虚拟机。    
  ![示例： 在仪表板中选择一个 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  在为虚拟机页上，单击**磁盘** > **附加现有**。   
  ![示例： 附加现有磁盘](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  在**附加现有磁盘**页上，单击**VHD 文件**。 **存储帐户**页将打开。    
  ![示例： 选择 VHD 文件](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  下**存储帐户**，选择要使用的帐户，然后选择保存以前上载的.vhd 文件的容器。 选择.vhd 文件，然后单击**选择**以保存所选内容。    
  ![示例： 选择容器](media/azure-stack-manage-vm-disks/select-container2.png)

7.  下**附加现有磁盘**，您选择的文件下列出**VHD 文件**。 更新**主机缓存**磁盘的设置，然后单击**确定**保存新 vm 的磁盘配置。    
  ![示例： 将附加的 VHD 文件](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  新磁盘 Azure 堆栈将创建磁盘，并将其附加到虚拟机后，被列入下的虚拟机的磁盘设置**数据磁盘**。   
  ![示例： 完成磁盘附加](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>使用 PowerShell 将多个非托管的磁盘添加到 VM
你可以使用 PowerShell 预配 VM，然后添加新的数据磁盘或附加预先存在的**.vhd**文件作为数据磁盘。

**添加 AzureRmVMDataDisk** cmdlet 将数据磁盘添加到虚拟机。 当你创建虚拟机，也可以将数据磁盘添加到现有的虚拟机，你可以添加数据磁盘。 指定**VhdUri**参数来分发到不同的容器的磁盘。

### <a name="add-data-disks-to-a-new-virtual-machine"></a>将数据磁盘添加到新的虚拟机
下面的示例使用 PowerShell 命令来创建具有三个数据磁盘的 VM，每个放置在不同的容器。

第一个命令创建一个虚拟机对象，并将其在*$VirtualMachine*变量。 该命令将分配给虚拟机的名称和大小。
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

接下来三个命令将三个数据磁盘添加到的路径分配给*$DataDiskVhdUri01*， *$DataDiskVhdUri02*，和*$DataDiskVhdUri03*变量。 在要分发到不同的容器的磁盘的 URL 中定义的不同路径名称。     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

最终的三个命令将数据磁盘添加到虚拟机存储在*$VirtualMachine*。 每个命令指定名称、 位置和磁盘的其他属性。 每个磁盘的 URI 存储在*$DataDiskVhdUri01*， *$DataDiskVhdUri02*，和*$DataDiskVhdUri03*。
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

使用以下 PowerShell 命令添加到该 VM，OS 磁盘和网络配置，然后启动新的 VM。
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>将数据磁盘添加到现有的虚拟机
下面的示例使用 PowerShell 命令将三个数据磁盘添加到现有 VM。
第一个命令获取名 VirtualMachine 为使用的虚拟机**Get AzureRmVM** cmdlet。 该命令在 *$VirtualMachine* 变量中存储虚拟机。
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
接下来三个命令将三个数据磁盘的路径分配给 $DataDiskVhdUri01、 $DataDiskVhdUri02 和 $DataDiskVhdUri03 变量。  Vhduri 中的不同路径名称指示磁盘放置虚拟机的不同容器。
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  接下来三个命令将数据磁盘添加到虚拟机存储在*$VirtualMachine*变量。 每个命令指定名称、 位置和磁盘的其他属性。 每个磁盘的 URI 存储在*$DataDiskVhdUri01*， *$DataDiskVhdUri02*，和*$DataDiskVhdUri03*。
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  最后一个命令将更新存储在虚拟机的状态*$VirtualMachine*在-*ResourceGroupName*。
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 堆栈的虚拟机的详细信息，请参阅[Azure 堆栈中的虚拟机的注意事项](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)。
