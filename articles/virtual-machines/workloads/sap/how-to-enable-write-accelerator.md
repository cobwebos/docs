---
title: 用于 SAP 部署的 Azure 写入加速器 | Microsoft Docs
description: Azure 虚拟机上部署的 SAP HANA 系统的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89a4216a3893892eedd6c216c7e0e5d51cf64749
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>用于 SAP 部署的 Azure 写入加速器
Azure 写入加速器是专门针对 M 系列 VM 推出的一项功能。 Azure 写入加速器不适用于 Azure 中的其他任何 VM 系列，而只适用于 M 系列。 顾名思义，该功能的目的是改善针对 Azure 高级存储执行的写入操作的 I/O 延迟。 

公共预览版 Azure 写入加速器功能已在以下区域中可用于 M 系列部署：

- 美国西部 2
- 美国东部 2
- 欧洲西部
- 东南亚

## <a name="planning-for-using-azure-write-accelerator"></a>使用 Azure 写入加速器进行规划
应该对包含事务日志或 DBMS 重做日志的卷使用 Azure 写入加速器。 不建议对 DBMS 的数据卷使用 Azure 写入加速器。 实施此限制的原因是，Azure 写入加速器要求装载 Azure 高级存储 VHD，并且不能使用适用于高级存储的 附加读取缓存。 这种缓存的优势更多地体现在传统数据库中。 由于写入加速器只会影响写入活动，而不会加快读取，因此，支持的 SAP 设计是针对 SAP 支持的数据库的事务日志或重做日志驱动器使用写入加速器。 

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 因此，需要做好相应的规划。 

>[!NOTE]
> 由于 Azure 写入加速器的功能目前仍以公共预览版提供，该功能尚不支持任何生产场景部署。

Azure 写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：


| VM SKU | 写入加速器磁盘数 | 每个 VM 的写入加速器 IOPS |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 



> [!IMPORTANT]
> 若要针对基于多个 Azure 高级存储磁盘构建的现有卷，或者使用 Windows 磁盘或卷管理器、Windows 存储空间、Windows 横向扩展文件服务器 (SOFS)、Linux LVM 或 MDADM 的条带化卷启用或禁用 Azure 写入加速器，必须通过单独的步骤，对构成该卷的所有磁盘启用或禁用写入加速器。 **在此类配置中启用或禁用写入加速器之前，请关闭 Azure VM**。 


> [!IMPORTANT]
> 若要针对不属于基于多个磁盘构建的，且使用 Windows 磁盘或卷管理器、Windows 存储空间、Windows 横向扩展文件服务器 (SOFS)、Linux LVM 或 MDADM 的现有 Azure 磁盘启用 Azure 写入加速器，需要关闭访问该 Azure 磁盘的工作负荷。 必须关闭使用 Azure 磁盘的数据库应用程序。

> [!IMPORTANT]
> 对 VM 的 Azure VM 操作系统磁盘启用写入加速器会重新启动该 VM。 

对于 SAP 相关的 VM 配置，应该不需要为操作磁盘启用 Azure 写入加速器

### <a name="restrictions-when-using-azure-write-accelerator"></a>使用 Azure 写入加速器时的限制
对 Azure 磁盘/VHD 使用 Azure 写入加速器时，需遵循以下限制：

- 需要将高级磁盘缓存设置为“无”或“只读”。 不支持其他所有缓存模式。
- 尚不支持已启用写入加速器的磁盘上的快照。 此限制会导致 Azure 备份服务无法对虚拟机的所有磁盘执行应用程序一致的快照。
- 仅较小的 I/O 大小会采用加速路径。 在以下工作负荷情形下，I/O 写入到磁盘的更改不会采用加速路径：数据大容量加载或者数据在持久保存到存储之前，不同 DBMS 的事务日志缓冲区已较大程度上填满。


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>在特定磁盘上启用写入加速器
以下几个部分介绍如何在 Azure 高级存储 VHD 上启用 Azure 写入加速器。

目前，只能通过 Azure REST API 和 PowerShell 启用写入加速器。 在数周之后，将会支持其他方法，包括使用 Azure 门户的方法。

### <a name="prerequisites"></a>先决条件
目前，使用 Azure 写入加速器必须满足以下先决条件：

- 需将用于部署 VM 和 VM 存储的订阅 ID 加入白名单。 请联系 Microsoft CSA、GBB 或客户经理将你的订阅 ID 加入白名单。 
- 要向其应用 Azure 写入加速器的磁盘需是 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。

### <a name="enabling-through-power-shell"></a>通过 PowerShell 启用
Azure PowerShell 模块 5.5.0 和更高版本对相关的 cmdlet 做了更改，以便能够针对特定的 Azure 高级存储磁盘启用或禁用 Azure 写入加速器。
为了启用或部署写入加速器支持的磁盘，以下 PowerShell 命令已发生更改，并已扩展为接受写入加速器的参数。

已将新的开关参数“WriteAccelerator”添加到以下 cmdlet： 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

不指定该参数会将属性设置为 false，并且会部署不受 Azure 写入加速器支持的磁盘。

已将新的开关参数“OsDiskWriteAccelerator”添加到以下 cmdlet： 

- Set-AzureRmVmssStorageProfile

不指定该参数会将属性设置为 false，并且会提供不利用 Azure 写入加速器的磁盘。

已将新的可选布尔参数（不可为 null）“OsDiskWriteAccelerator”添加到以下 cmdlet： 

- Update-AzureRmVM
- Update-AzureRmVmss

指定 $true 或 $false 可以控制磁盘对 Azure 写入加速器的支持。

示例命令如下所示：

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

以下部分了演示了两个主要场景的脚本编写方式。

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>添加 Azure 写入加速器支持的新磁盘
可以使用此脚本将新磁盘添加到 VM。 使用此脚本创建的磁盘将使用 Azure 写入加速器。

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
需要根据特定的部署，调整 VM、磁盘、资源组的名称，以及磁盘的大小和 LunID。


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>在现有 Azure 磁盘上启用 Azure 写入加速器
如果需要在现有磁盘上启用写入加速器，可以使用此脚本来执行该任务：

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

需要调整 VM、磁盘和资源组的名称。 上述脚本将写入加速器添加到 $newstatus 值设置为 $true 的现有磁盘。 使用 $false 值会在给定的磁盘上禁用写入加速器。

> [!Note]
> 执行上述脚本会分离指定的磁盘，针对该磁盘启用写入加速器，然后重新附加该磁盘




### <a name="enabling-through-rest-apis"></a>通过 REST API 启用
若要通过 Azure REST API 进行部署，需要安装 Azure armclient

#### <a name="install-armclient"></a>安装 armclient

若要运行 armclient，需要通过 Chocolatey 安装它。 可以通过 cmd.exe 或 PowerShell 来安装它。 使用提升的权限执行这些命令（“以管理员身份运行”）。

使用 cmd.exe 运行以下命令：

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

如果使用 PowerShell，必须运行：

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

现在，可以在 cmd.exe 或 PowerShell 中使用以下命令安装 armclient

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>获取当前的 VM 配置
若要更改磁盘配置的属性，首先需要获取 JSON 文件中的当前配置。 可以执行以下命令来获取当前配置：

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
将“<<   >>”中的内容替换为自己的数据，包括 JSON 文件应使用的文件名。

输出可能如下所示：

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

下一步是更新 JSON 文件，并在名为“log1”的磁盘上启用写入加速器。 可以通过将此属性添加到 JSON 文件中磁盘缓存条目的后面来完成此步骤。 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

然后使用以下命令更新现有部署：

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

输出应如下所示。 可以看到，为一个磁盘启用了写入加速器。

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

进行这项更改后，写入加速器应会支持该驱动器。

 