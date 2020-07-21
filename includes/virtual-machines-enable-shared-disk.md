---
title: 包含文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29a90b94db5e6e5791361bad004efcf649e1950b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500580"
---
## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

共享磁盘支持多个操作系统。 有关支持的操作系统的概念文章，请参阅[Windows](../articles/virtual-machines/windows/disks-shared.md#windows)和[Linux](../articles/virtual-machines/linux/disks-shared.md#linux)部分。

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>部署共享磁盘

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>将高级 SSD 部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请使用新属性 `maxShares` 并定义大于1的值。 这会使该磁盘可在多个 Vm 间共享。

> [!IMPORTANT]
> `maxShares`仅当从所有 vm 中卸载磁盘时，才能设置或更改的值。 请参阅[磁盘大小](#disk-sizes)以了解的允许值 `maxShares` 。

#### <a name="cli"></a>CLI
```azurecli

az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku PremiumSSD_LRS --max-shares 2

```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType PremiumSSD_LRS -CreateOption Empty

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure 资源管理器
使用以下模板之前，请 `[parameters('dataDiskName')]` 将、 `[resourceGroup().location]` 、 `[parameters('dataDiskSizeGB')]` 和替换 `[parameters('maxShares')]` 为自己的值。

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>将超磁盘部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请将 `maxShares` 参数更改为大于1的值。 这会使该磁盘可在多个 Vm 间共享。

> [!IMPORTANT]
> `maxShares`仅当从所有 vm 中卸载磁盘时，才能设置或更改的值。 请参阅[磁盘大小](#disk-sizes)以了解的允许值 `maxShares` 。

#### <a name="cli"></a>CLI
```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure 资源管理器

若要部署启用了共享磁盘功能的托管磁盘，请使用属性 `maxShares` 并定义大于1的值。 这会使该磁盘可在多个 Vm 间共享。

> [!IMPORTANT]
> `maxShares`仅当从所有 vm 中卸载磁盘时，才能设置或更改的值。 请参阅[磁盘大小](#disk-sizes)以了解的允许值 `maxShares` 。

使用以下模板之前，请将、、、、、、 `[parameters('dataDiskName')]` `[resourceGroup().location]` 和替换 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 为自己的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>将 Azure 共享磁盘与 Vm 配合使用

使用部署共享磁盘后 `maxShares>1` ，可以将该磁盘装载到一台或多台 vm。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>支持的 SCSI PR 命令

将共享磁盘装载到群集中的 Vm 后，可以使用 SCSI PR 建立仲裁和对磁盘的读/写。 使用 Azure 共享磁盘时，可以使用以下 PR 命令：

若要与磁盘进行交互，请从持久保留操作列表开始：

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

使用 PR_RESERVE、PR_PREEMPT_RESERVATION 或 PR_RELEASE_RESERVATION 时，请提供以下持久性保留类型之一：

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

当使用 PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION 或 PR_RELEASE 保留时，还需要提供永久性保留密钥。


## <a name="next-steps"></a>后续步骤
