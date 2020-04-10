---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008616"
---
Azure 超级磁盘为 Azure IaaS 虚拟机 （VM） 提供高吞吐量、高 IOPS 和一致的低延迟磁盘存储。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超磁盘的一个主要好处是能够动态更改 SSD 的性能以及您的工作负载，而无需重新启动 VM。 超级磁盘适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。

## <a name="ga-scope-and-limitations"></a>GA 范围和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>确定 VM 大小和地区可用性

### <a name="vms-using-availability-zones"></a>使用可用性区域的 VM

要利用超磁盘，您需要确定您位于哪个可用性区域。 并不是每个区域都支持每个 VM 大小与超磁盘。 要确定区域、区域和 VM 大小是否支持超磁盘，请运行以下任一命令，请确保首先替换**区域****、vmSize**和**订阅**值：

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

响应将类似于下面的窗体，其中 X 是用于在所选区域中部署的区域。 X 可能是1、2 或 3。

保留 **"区域"** 值，它表示可用性区域，您需要它才能部署 Ultra 磁盘。

|ResourceType  |“属性”  |位置  |区域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令没有响应，则所选区域中的超磁盘不支持所选 VM 大小。

现在，您已经了解了要部署到哪个区域，请按照本文中的部署步骤部署附加超磁盘的 VM 或将超磁盘附加到现有 VM。

### <a name="vms-with-no-redundancy-options"></a>没有冗余选项的 VM

目前，部署在美国西部部署的超磁盘必须没有任何冗余选项。 但是，并非所有支持超磁盘的磁盘大小都位于此区域。 要确定美国西部的哪些磁盘支持超磁盘，可以使用以下代码段之一。 请确保首先替换 和`vmSize``subscription`值：

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

响应将类似于以下形式，`UltraSSDAvailable   True`指示 VM 大小是否支持此区域中的超磁盘。

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure 资源管理器部署超磁盘

首先，确定要部署的 VM 大小。 有关支持的 VM 大小列表，请参阅[GA 范围和限制](#ga-scope-and-limitations)部分。

如果要创建具有多个超磁盘的 VM，请参阅示例["创建具有多个超磁盘的 VM"。](https://aka.ms/ultradiskArmTemplate)

如果要使用自己的模板，请确保**apiVersion**设置为`Microsoft.Compute/virtualMachines``Microsoft.Compute/Disks` `2018-06-01` （或更高版本）。

将磁盘 sKU 设置为**UltraSSD_LRS**，然后在 MBps 中设置磁盘容量、IOPS、可用性区域和吞吐量以创建超磁盘。

在预配 VM 后，可以对数据磁盘进行分区和格式设置并为工作负荷配置这些磁盘。


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 门户部署超磁盘

本节介绍部署配备超磁盘作为数据磁盘的虚拟机。 它假定您熟悉部署虚拟机（如果不熟悉），请参阅我们的[快速入门：在 Azure 门户中创建 Windows 虚拟机](../articles/virtual-machines/windows/quick-create-portal.md)。

- 登录到 Azure[门户](https://portal.azure.com/)并导航以部署虚拟机 （VM）。
- 请确保选择[受支持的 VM 大小和区域](#ga-scope-and-limitations)。
- 在**可用性选项**中选择**可用性区域**。
- 使用您选择的选项填写其余条目。
- 选择“磁盘”****。

![创建启用超磁盘的 vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 在磁盘边栏选项卡上，选择 **"是****"以启用超磁盘兼容性**。
- 选择 **"立即创建并附加新磁盘**以连接超磁盘"。

![启用和附加超磁盘.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- 在"**创建新磁盘**边栏选项卡"上，输入名称，然后选择 **"更改大小**"。
- 将**帐户类型**更改为**超级磁盘**。
- 将**自定义磁盘大小 （GiB）**、**磁盘 IOPS**和**磁盘吞吐量**的值更改为您选择的值。
- 在两个刀片中选择 **"确定**"。
- 继续 VM 部署，它将与部署任何其他 VM 相同。

![创建超磁盘.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 门户连接超磁盘

或者，如果现有 VM 位于能够使用超磁盘的区域/可用性区域中，则可以使用超磁盘，而无需创建新 VM。 通过在现有 VM 上启用超磁盘，然后将它们附加到数据磁盘。

- 导航到 VM 并选择**磁盘**。
- 选择 **"编辑**"。

![选项-选择器-超磁盘.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- 选择 **"是****"以启用超磁盘兼容性**。

![超选项-是启用.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- 选择“保存”。 
- 选择 **"添加数据磁盘**"，然后在**名称**选择"**创建磁盘**"的下拉下列表中选择。

![创建和附加-新超磁盘.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 填写新磁盘的名称，然后选择 **"更改大小**"。
- 将**帐户类型**更改为**超级磁盘**。
- 将**自定义磁盘大小 （GiB）**、**磁盘 IOPS**和**磁盘吞吐量**的值更改为您选择的值。
- 选择 **"确定"，** 然后选择 **"创建**"。

![制作新超磁盘.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- 返回到磁盘的边栏选项卡后，选择 **"保存**"。

![保存和附加-新超磁盘.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>使用 Azure 门户调整超磁盘的性能

超磁盘具有独特的功能，允许您调整其性能。 可以从 Azure 门户、磁盘本身进行这些调整。

- 导航到 VM 并选择**磁盘**。
- 选择要修改性能的超磁盘。

![选择超磁盘到修改.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- 选择 **"配置**"，然后进行修改。
- 选择“保存”。 

![配置超磁盘性能和大小.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>使用 CLI 部署超磁盘

首先，确定要部署的 VM 大小。 有关支持的 VM 大小的列表，请参阅[GA 范围和限制](#ga-scope-and-limitations)部分。

您必须创建能够使用超磁盘的 VM，以便附加超磁盘。

用您自己的值替换或设置 **$vmname**$vmname、$rgname、$diskname、$location、$password、$user变量。 **$rgname** **$diskname** **$location** **$password** **$user** $zone**集**到[从本文开头](#determine-vm-size-and-region-availability)获得的可用性区域的值。 然后运行以下 CLI 命令以创建启用的超启用 VM：

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>在现有 VM 上启用超磁盘兼容性

如果您的 VM 满足[GA 范围和限制](#ga-scope-and-limitations)中概述的要求，并且位于[帐户的相应区域](#determine-vm-size-and-region-availability)中，则可以在 VM 上启用超磁盘兼容性。

要启用超磁盘兼容性，必须停止 VM。 停止 VM 后，可以启用兼容性、附加超磁盘，然后重新启动 VM：

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>使用 CLI 创建超磁盘

现在，您拥有了能够附加超磁盘的 VM，您可以创建超级磁盘并将其附加到它。

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>使用 CLI 将超磁盘连接到 VM

或者，如果现有 VM 位于能够使用超磁盘的区域/可用性区域中，则可以使用超磁盘，而无需创建新 VM。

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>使用 CLI 调整超磁盘的性能

Ultra 磁盘提供了独特的功能，允许您调整其性能，以下命令描述了如何使用此功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>使用 PowerShell 部署超磁盘

首先，确定要部署的 VM 大小。 有关支持的 VM 大小的列表，请参阅[GA 范围和限制](#ga-scope-and-limitations)部分。

要使用超磁盘，必须创建能够使用超磁盘的 VM。 用您自己的值替换或设置 **$resourcegroup$vmName**变量。 **$resourcegroup** $zone**集**到[从本文开头](#determine-vm-size-and-region-availability)获得的可用性区域的值。 然后运行以下[New-AzVm](/powershell/module/az.compute/new-azvm)命令以创建启用的超启用 VM：

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>在现有 VM 上启用超磁盘兼容性

如果您的 VM 满足[GA 范围和限制](#ga-scope-and-limitations)中概述的要求，并且位于[帐户的相应区域](#determine-vm-size-and-region-availability)中，则可以在 VM 上启用超磁盘兼容性。

要启用超磁盘兼容性，必须停止 VM。 停止 VM 后，可以启用兼容性、附加超磁盘，然后重新启动 VM：

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>使用 PowerShell 创建超磁盘

现在，您拥有了能够使用超磁盘的 VM，您可以创建超级磁盘并将其附加到它：

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>使用 PowerShell 将超磁盘连接到 VM

或者，如果现有 VM 位于能够使用超磁盘的区域/可用性区域中，则可以使用超磁盘，而无需创建新 VM。

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 调整超磁盘的性能

Ultra 磁盘具有独特的功能，允许您调整其性能，以下命令是一个无需分离磁盘即可调整性能的示例：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```