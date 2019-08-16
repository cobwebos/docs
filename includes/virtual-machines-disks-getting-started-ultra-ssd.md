---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: db8147717e825d9cc48b7f0704dc5eea0be223a9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510320"
---
# <a name="using-azure-ultra-disks"></a>使用 Azure 超磁盘

Azure ultra 磁盘为 Azure IaaS 虚拟机 (Vm) 提供高吞吐量、高 IOPS 和一致的低延迟磁盘存储。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超磁盘的一个主要优点是能够在不重新启动 Vm 的情况下动态更改 SSD 的性能和工作负荷。 超磁盘适用于数据密集型工作负荷, 例如 SAP HANA、顶级数据库和事务密集型工作负荷。

## <a name="check-if-your-subscription-has-access"></a>检查你的订阅是否有访问权限

如果你已注册了 ultra 磁盘, 并且想要检查是否为超磁盘启用了你的订阅, 请使用以下命令之一: 

CLI：`az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell：`Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

如果你的订阅已启用, 则输出应如下所示:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>确定可用性区域

批准后, 需要确定你所在的可用性区域, 以便使用超磁盘。 运行以下任一命令以确定要将您的 ultra 磁盘部署到哪个区域, 确保首先替换**region**、 **vmSize**和**订阅**值:

CLI：

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell：

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

响应将类似于以下形式, 其中 X 是用于在所选区域中进行部署的区域。 X 可能是1、2 或 3。 目前, 只有三个区域支持超小型磁盘, 它们是:美国东部2、东南亚和北欧。

保留 "**区域**" 值, 它表示可用性区域, 你需要它来部署超磁盘。

|ResourceType  |姓名  |Location  |区域  |限制  |功能  |ReplTest1  |
|---------|---------|---------|---------|---------|---------|---------|
|磁盘     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令没有响应, 则可能是该功能的注册仍处于挂起状态, 或者使用的是旧版本的 CLI 或 PowerShell。

现在, 你已了解要部署到哪个区域, 请按照本文中的部署步骤, 使用附加的 ultra 磁盘部署 VM, 或者将超小型磁盘附加到现有 VM。

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure 资源管理器部署 ultra 磁盘

首先, 确定要部署的 VM 大小。 目前, 只有 DsV3 和 EsV3 VM 系列支持 ultra 磁盘。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。

若要创建具有多个超磁盘的 VM, 请参阅示例[创建包含多个超磁盘的 vm](https://aka.ms/UltraSSDTemplate)。

如果要使用自己的模板, 请确保将**apiVersion** `Microsoft.Compute/virtualMachines` `Microsoft.Compute/Disks`设置`2018-06-01`为 (或更高版本)。

将磁盘 sku 设置为**UltraSSD_LRS**, 然后设置磁盘容量、IOPS、可用性区域和吞吐量 (以 MBps 为单位) 以创建超磁盘。

在预配 VM 后，可以对数据磁盘进行分区和格式设置并为工作负荷配置这些磁盘。

## <a name="deploy-an-ultra-disk-using-cli"></a>使用 CLI 部署 ultra 磁盘

首先, 确定要部署的 VM 大小。 目前, 只有 DsV3 和 EsV3 VM 系列支持 ultra 磁盘。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。

必须创建能够使用 ultra 磁盘的 VM, 才能附加超磁盘。

将 **$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user**变量替换为你自己的值。 将 **$zone**设置为从[本文开头](#determine-your-availability-zone)获取的可用性区域值。 然后运行以下 CLI 命令, 以创建支持 ultra 的 VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>使用 CLI 创建超小型磁盘

现在, 你有了一个能够附加超磁盘的 VM, 你可以创建一个超磁盘并将其附加到该磁盘。

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>使用 CLI 将 ultra 磁盘附加到 VM

或者, 如果你的现有 VM 位于能够使用超磁盘的区域/可用性区域中, 则可以使用超磁盘, 而不必创建新的 VM。

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>使用 CLI 调整超磁盘的性能

超磁盘提供了独特的功能, 使你能够调整性能, 以下命令描述了如何使用此功能:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>使用 PowerShell 部署 ultra 磁盘

首先, 确定要部署的 VM 大小。 目前, 只有 DsV3 和 EsV3 VM 系列支持 ultra 磁盘。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。

若要使用超磁盘, 必须创建能够使用超磁盘的 VM。 将 **$resourcegroup**和 **$vmName**变量替换为自己的值。 将 **$zone**设置为从[本文开头](#determine-your-availability-zone)获取的可用性区域值。 然后运行以下[new-azvm](/powershell/module/az.compute/new-azvm)命令, 以创建支持 HYPER-V 的 VM:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>使用 PowerShell 创建超小型磁盘

现在, 你有了一个能够使用超磁盘的 VM, 接下来可以创建一个超磁盘并将其附加到其中:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>使用 PowerShell 将 ultra 磁盘附加到 VM

或者, 如果你的现有 VM 位于能够使用超磁盘的区域/可用性区域中, 则可以使用超磁盘, 而不必创建新的 VM。

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

超磁盘具有独特的功能, 使你能够调整性能, 以下命令是一个示例, 用于在不分离磁盘的情况下调整性能:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>后续步骤

如果希望使用此调查尝试新的磁盘类型[请求访问权限](https://aka.ms/UltraDiskSignup)。