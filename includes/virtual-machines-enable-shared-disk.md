---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471666"
---
## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>部署 Azure 共享磁盘

要部署启用共享磁盘功能的托管磁盘，请使用新属性`maxShares`并定义值`>1`。 这使得磁盘可跨多个 VM 共享。

> [!IMPORTANT]
> 仅当从所有`maxShares`VM 卸载磁盘时，才能设置或更改 的值。 有关 的允许值，请参阅[磁盘大小](#disk-sizes)`maxShares`。

在使用以下模板之前，请`[parameters('dataDiskName')]``[parameters('maxShares')]`用`[resourceGroup().location]`您自己的`[parameters('dataDiskSizeGB')]`值替换 、和。

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>将 Azure 共享磁盘与 VM 一起使用

使用`maxShares>1`中部署了共享磁盘后，可以将磁盘装载到一个或多个 VM。

> [!IMPORTANT]
> 共享磁盘的所有 VM 都必须部署在同一[接近放置组中](../articles/virtual-machines/windows/proximity-placement-groups.md)。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>支持的 SCSI PR 命令

将共享磁盘安装到群集中的 VM 后，可以使用 SCSI PR 建立仲裁并读取/写入磁盘。 使用 Azure 共享磁盘时，可以使用以下 PR 命令：

要与磁盘进行交互，从持久保留操作列表开始：

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

使用PR_RESERVE、PR_PREEMPT_RESERVATION或PR_RELEASE_RESERVATION时，提供以下持久保留类型之一：

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

使用PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION或PR_RELEASE-保留时，还需要提供持久保留密钥。


## <a name="next-steps"></a>后续步骤

如果您有兴趣尝试共享磁盘，[请注册我们的预览](https://aka.ms/AzureSharedDiskPreviewSignUp)版。