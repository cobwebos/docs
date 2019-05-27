---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 326382339e2b4aeaa488d3d7f76b7ff35f9bc620
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147780"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>启用并部署 Azure 超高 Ssd （预览版）

Azure 超高的固态驱动器 (SSD) （预览） 产品/服务的高吞吐量、 高 IOPS 和一致的低延迟磁盘存储的 Azure IaaS 虚拟机 (Vm)。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超高 Ssd 的一个主要优势是能够动态地更改而无需重新启动 Vm 工作负荷以及 SSD 的性能。 超高 Ssd 适合数据密集型工作负荷，例如 SAP HANA、 顶层数据库和事务密集型工作负荷。

目前，超高 Ssd 处于预览状态，您必须[注册](https://aka.ms/UltraSSDPreviewSignUp)才能访问其在预览中。

## <a name="determine-your-availability-zone"></a>确定可用性区域

批准后，您需要确定哪些可用性区域中，是为了使用超高 Ssd。 运行以下命令，以确定在美国东部 2 将部署到超高磁盘的区域之一：

PowerShell：`Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI：`az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

响应将类似于下面的表格，其中 X 是要用于部署在美国东部 2 区域。 X 可能是1、2 或 3。

保留**区域**值，它表示可用性区域，并将它将部署所需的超高 SSD。

|ResourceType  |名称  |Location  |区域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|磁盘     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

如果从命令中，没有响应，则你的注册功能仍为挂起，或不已尚未批准。

在知道了要部署到哪个区域后，请按照本文中的部署步骤来部署第一个具有超级 SSD 的 VM。

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>部署使用 Azure 资源管理器的超高 SSD

首先，确定要部署的 VM 大小。 在此预览版中，仅支持 DsV3 和 EsV3 VM 系列。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。

如果你想要创建具有多个超 Ssd 的 VM，请参阅示例[创建具有多个超 SSD 的 VM](https://aka.ms/UltraSSDTemplate)。

如果你想要使用你自己的模板，请确保**apiVersion**有关`Microsoft.Compute/virtualMachines`并`Microsoft.Compute/Disks`设置为`2018-06-01`（或更高版本）。

设置为磁盘 sku **UltraSSD_LRS**，然后以 mbps 为单位来创建超高磁盘设置磁盘容量、 IOPS、 可用性区域和吞吐量。

在预配 VM 后，可以对数据磁盘进行分区和格式设置并为工作负荷配置这些磁盘。

## <a name="deploy-an-ultra-ssd-using-cli"></a>部署使用 CLI 的超高 SSD

首先，确定要部署的 VM 大小。 在此预览版中，仅支持 DsV3 和 EsV3 VM 系列。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。

若要使用超高的 Ssd，必须创建的 VM，可以使用超高 Ssd。

替换或设置 **$vmname**， **$rgname**， **$diskname**， **$location**， **$password**， **$user**具有自己的值的变量。 设置 **$zone**的值时所获取的在可用性区域[这篇文章的开始](#determine-your-availability-zone)。 然后运行以下 CLI 命令创建的超高的已启用的 VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>创建使用 CLI 的超高 SSD

现在，已可以使用超高 Ssd 的 VM，可以创建并向其附加超高 SSD。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>调整使用 CLI 的超高 SSD 的性能

超高 Ssd 提供独特的功能，您可以调整它们的性能，下面的命令描述了如何使用此功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>部署使用 PowerShell 的超高 SSD

首先，确定要部署的 VM 大小。 在此预览版中，仅支持 DsV3 和 EsV3 VM 系列。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。

若要使用超高的 Ssd，必须创建的 VM，可以使用超高 Ssd。 替换或设置 **$resourcegroup**并 **$vmName**具有自己的值的变量。 设置 **$zone**的值时所获取的在可用性区域[这篇文章的开始](#determine-your-availability-zone)。 然后运行以下[New-azvm](/powershell/module/az.compute/new-azvm)命令以创建超高启用 VM:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>创建使用 PowerShell 的超高 SSD

现在，已可以使用超高 Ssd 的 VM，你可以创建并附加到它的超高 SSD:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>调整使用 PowerShell 的超高 SSD 的性能

超高 Ssd 有独特的功能，您可以调整它们的性能，以下命令是一个示例，而无需分离该磁盘调整性能：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>后续步骤

如果你想要尝试新的磁盘类型[请求访问预览版使用此调查](https://aka.ms/UltraSSDPreviewSignUp)。