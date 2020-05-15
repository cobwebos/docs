---
title: 教程 - 通过 Azure PowerShell 创建和使用规模集的磁盘
description: 了解如何通过 Azure PowerShell 对虚拟机规模集创建和使用托管磁盘，包括如何添加、准备、列出和分离磁盘。
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 5c82f087505c1634dd621252935c4017687340b2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198245"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>教程：通过 Azure PowerShell 对虚拟机规模集创建和使用磁盘

虚拟机规模集使用磁盘来存储 VM 实例的操作系统、应用程序和数据。 创建和管理规模集时，请务必选择适用于所需工作负荷的磁盘大小和配置。 本教程介绍如何创建和管理 VM 磁盘。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * OS 磁盘和临时磁盘
> * 数据磁盘数
> * 标准磁盘和高级磁盘
> * 磁盘性能
> * 附加和准备数据磁盘

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>默认 Azure 磁盘
创建或缩放规模集时，会自动将两个磁盘附加到每个 VM 实例。 

**操作系统磁盘** - 操作系统磁盘大小可达 2 TB，并可托管 VM 实例的操作系统。 默认情况下，OS 磁盘标记为“/dev/sda”  。 已针对 OS 性能优化了 OS 磁盘的磁盘缓存配置。 由于此配置，OS 磁盘不应  托管应用程序或数据。 对于应用程序和数据，请使用数据磁盘，本文后面会对其进行详细介绍。 

**临时磁盘** - 临时磁盘使用 VM 实例所在的 Azure 主机上的固态硬盘。 这些磁盘具有高性能，可用于临时数据处理等操作。 但是，如果将 VM 实例移到新的主机，临时磁盘上存储的数据都会删除。 临时磁盘的大小由 VM 实例大小决定。 临时磁盘标记为“/dev/sdb”  ，且装载点为 /mnt  。

### <a name="temporary-disk-sizes"></a>临时磁盘大小
| 类型 | 常见大小 | 临时磁盘大小上限 (GiB) |
|----|----|----|
| [常规用途](../virtual-machines/windows/sizes-general.md) | A、B、D 系列 | 1600 |
| [计算优化](../virtual-machines/windows/sizes-compute.md) | F 系列 | 576 |
| [内存优化](../virtual-machines/windows/sizes-memory.md) | D、E、G、M 系列 | 6144 |
| [存储优化](../virtual-machines/windows/sizes-storage.md) | L 系列 | 5630 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | N 系列 | 1440 |
| [高性能](../virtual-machines/windows/sizes-hpc.md) | A 和 H 系列 | 2000 |


## <a name="azure-data-disks"></a>Azure 数据磁盘
可添加额外的数据磁盘，用于安装应用程序和存储数据。 在任何需要持久和响应性数据存储的情况下，都应使用数据磁盘。 每个数据磁盘的最大容量为 4 TB。 VM 实例的大小决定可附加的数据磁盘数。 对于每个 VM vCPU，都可以附加两个数据磁盘。

### <a name="max-data-disks-per-vm"></a>每个 VM 的最大数据磁盘数
| 类型 | 常见大小 | 每个 VM 的最大数据磁盘数 |
|----|----|----|
| [常规用途](../virtual-machines/windows/sizes-general.md) | A、B、D 系列 | 64 |
| [计算优化](../virtual-machines/windows/sizes-compute.md) | F 系列 | 64 |
| [内存优化](../virtual-machines/windows/sizes-memory.md) | D、E、G、M 系列 | 64 |
| [存储优化](../virtual-machines/windows/sizes-storage.md) | L 系列 | 64 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | N 系列 | 64 |
| [高性能](../virtual-machines/windows/sizes-hpc.md) | A 和 H 系列 | 64 |


## <a name="vm-disk-types"></a>VM 磁盘类型
Azure 提供两种类型的磁盘。

### <a name="standard-disk"></a>标准磁盘
标准存储受 HDD 支持，可以在确保性能的同时提供经济高效的存储。 标准磁盘适用于经济高效的开发和测试工作负荷。

### <a name="premium-disk"></a>高级磁盘
高级磁盘由基于 SSD 的高性能、低延迟磁盘提供支持。 建议对运行生产工作负荷的 VM 使用这些磁盘。 高级存储支持 DS 系列、DSv2 系列、GS 系列和 FS 系列 VM。 选择磁盘大小时，大小值将舍入为下一类型。 例如，如果磁盘大小小于 128 GB，则磁盘类型为 P10。 如果磁盘大小介于 129 GB 和 512 GB 之间，则大小为 P20。 如果超过 512 GB，则大小为 P30。

### <a name="premium-disk-performance"></a>高级磁盘性能
|高级存储磁盘类型 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 磁盘大小（向上舍入） | 32 GB | 64 GB | 128 GB | 512 GB | 1,024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| 每个磁盘的最大 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
每个磁盘的吞吐量 | 25 MB/秒 | 50 MB/秒 | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 | 250 MB/秒 | 250 MB/秒 |

尽管上表确定了每个磁盘的最大 IOPS，但还可通过条带化多个数据磁盘实现更高级别的性能。 例如，Standard_GS5 VM 最多可实现 80,000 IOPS。 若要详细了解每个 VM 的最大 IOPS，请参阅 [Windows VM 大小](../virtual-machines/windows/sizes.md)。


## <a name="create-and-attach-disks"></a>创建并附加磁盘
可以在创建规模集时创建和附加磁盘，也可以对现有的规模集创建和附加磁盘。

### <a name="attach-disks-at-scale-set-creation"></a>创建规模集时附加磁盘
使用 [New-AzVmss](/powershell/module/az.compute/new-azvmss) 创建虚拟机规模集。 出现提示时，请提供 VM 实例的用户名和密码。 若要将流量分配到单独的 VM 实例，则还要创建负载均衡器。 负载均衡器包含的规则可在 TCP 端口 80 上分配流量，并允许 TCP 端口 3389 上的远程桌面流量，以及 TCP 端口 5985 上的 PowerShell 远程流量。

两个磁盘都是 `-DataDiskSizeGb` 参数创建的。 第一个磁盘的大小为 *64* GB，第二个磁盘的大小为 *128* GB。 出现提示时，请针对规模集中的 VM 实例提供自己的所需管理凭据：

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

创建和配置所有的规模集资源和 VM 实例需要几分钟时间。

### <a name="attach-a-disk-to-existing-scale-set"></a>将磁盘附加到现有规模集
还可以将磁盘附加到现有的规模集。 使用在上一步创建的规模集通过 [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk) 添加另一磁盘。 以下示例将另一个 *128* GB 的磁盘附加到现有规模集：

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>准备数据磁盘
已创建并附加到规模集 VM 实例的磁盘是原始磁盘。 将磁盘用于数据和应用程序之前，必须准备磁盘。 若要准备磁盘，需要创建分区、创建文件系统，并将其装载。

若要跨规模集中的多个 VM 实例自动完成此过程，可以使用 Azure 自定义脚本扩展。 此扩展可以在每个 VM 实例上以本地方式执行脚本，以便完成各种任务，例如准备附加的数据磁盘。 有关详细信息，请参阅[自定义脚本扩展概述](../virtual-machines/windows/extensions-customscript.md)。


以下示例在每个 VM 实例上执行来自 GitHub 示例存储库的脚本，使用的是 [Add-AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) 命令，该命令用于准备所有原始的附加数据磁盘：


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

若要确认磁盘是否已正确地准备好，请通过 RDP 连接到某个 VM 实例。 

首先，使用 [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer) 获取负载均衡器对象。 然后使用 [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig) 查看入站 NAT 规则。 NAT 规则列出了 RDP 侦听的每个 VM 实例的 *FrontendPort*。 最后，使用 [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) 获取负载均衡器的公共 IP 地址：


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

若要连接到 VM，请指定所需 VM 实例对应的你自己的公共 IP 地址和端口号，如前述命令所示。 出现提示时，输入创建规模集时使用的凭据。 如果使用 Azure Cloud Shell，请从本地 PowerShell 命令提示符或远程桌面客户端执行此步骤。 以下示例连接到 VM 实例 *1*：

```powershell
mstsc /v 52.168.121.216:50001
```

打开 VM 实例上的本地 PowerShell 会话并使用 [Get-disk](/powershell/module/storage/get-disk) 查看连接的磁盘：

```powershell
Get-Disk
```

以下示例输出显示三个磁盘已附加到 VM 实例。

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

检查 VM 实例上的文件系统和装载点，如下所示：

```powershell
Get-Partition
```

以下示例输出显示三个磁盘已分配了驱动器号：

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

规模集中每个 VM 实例上的磁盘是以同一方式自动准备的。 规模集进行纵向扩展时，所需数据磁盘会附加到新的 VM 实例。 自定义脚本扩展也会运行，以便自动准备磁盘。

关闭与 VM 实例的远程桌面连接会话。


## <a name="list-attached-disks"></a>列出附加的磁盘
若要查看有关附加到规模集的磁盘的信息，请使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)，如下所示：

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

在 *VirtualMachineProfile.StorageProfile* 属性下会显示 *DataDisks* 的列表。 还会显示有关磁盘大小、存储层和 LUN（逻辑单元号）的信息。 以下示例输出显示了有关三个附加到规模集的数据磁盘的详细信息：

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>分离磁盘
不再需要某个给定的磁盘时，可以将其从规模集中分离。 该磁盘会从规模集的所有 VM 实例中删除。 若要从规模集中分离某个磁盘，请使用 [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) 并指定磁盘的 LUN。 LUN 显示在上一部分的 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) 命令的输出中。 以下示例从规模集分离 LUN *3*：

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和磁盘，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组及其所有资源。 `-Force` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。 `-AsJob` 参数会使光标返回提示符处，不会等待操作完成。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>后续步骤
本教程介绍了如何通过 Azure PowerShell 为规模集创建和使用磁盘：

> [!div class="checklist"]
> * OS 磁盘和临时磁盘
> * 数据磁盘数
> * 标准磁盘和高级磁盘
> * 磁盘性能
> * 附加和准备数据磁盘

请继续学习下一教程，了解如何对规模集 VM 实例使用自定义映像。

> [!div class="nextstepaction"]
> [对规模集 VM 实例使用自定义映像](tutorial-use-custom-image-powershell.md)
