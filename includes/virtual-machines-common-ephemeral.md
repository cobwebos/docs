---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155502"
---
在本地虚拟机（VM）存储上创建暂时的 OS 磁盘，而不会将其保存到远程 Azure 存储。 临时 OS 磁盘适用于无状态工作负荷，其中应用程序可以容忍单个 VM 故障，但会受到 VM 部署时间或重新映像单个 VM 实例的影响。 通过临时 OS 磁盘，可以更低的读取/写入延迟到 OS 磁盘和更快的 VM 重置。 
 
临时磁盘的主要功能是： 
- 适用于无状态应用程序。
- 它们可与 Marketplace 映像和自定义映像一起使用。
- 能够快速将 Vm 和规模集实例重置或重新映像到原始启动状态。  
- 延迟较低，与临时磁盘类似。 
- 临时 OS 磁盘是免费的，操作系统磁盘不会产生存储费用。
- 它们在所有 Azure 区域中均可用。 
- [共享映像库](/azure/virtual-machines/linux/shared-image-galleries)支持临时 OS 磁盘。 
 

 
永久性和临时 OS 磁盘之间的主要区别：

|                             | 永久性 OS 磁盘                          | 临时 OS 磁盘                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁盘的大小限制      | 2 TiB                                                                                        | VM 大小或2TiB 的缓存大小，以较小者为准。 有关**GiB 中的缓存大小**，请[参阅 DS](../articles/virtual-machines/linux/sizes-general.md)、 [ES](../articles/virtual-machines/linux/sizes-memory.md)、 [M](../articles/virtual-machines/linux/sizes-memory.md)、 [FS](../articles/virtual-machines/linux/sizes-compute.md)和[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 支持的 VM 大小          | All                                                                                          | DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| 磁盘类型支持           | 托管和非托管 OS 磁盘                                                                | 仅限托管 OS 磁盘                                                               |
| 区域支持              | 所有区域                                                                                  | 所有区域                              |
| 数据暂留            | 写入 OS 磁盘的 OS 磁盘数据存储在 Azure 存储中                                  | 写入到 OS 磁盘的数据将存储到本地 VM 存储中，而不会持久保存到 Azure 存储中。 |
| 停止解除分配状态      | 可以停止释放 Vm 和规模集实例，并将其从停止解除分配状态重启 | 无法停止释放 Vm 和规模集实例                                  |
| 专用操作系统磁盘支持 | 是                                                                                          | 否                                                                                 |
| OS 磁盘大小调整              | 在创建 VM 期间和停止释放 VM 之后支持                                | 仅在创建 VM 的过程中受支持                                                  |
| 调整为新的 VM 大小   | OS 磁盘数据被保留                                                                    | OS 磁盘上的数据将被删除，操作系统会重新预配                                      |

## <a name="size-requirements"></a>大小要求

你可以部署 VM 和实例映像，最大大小为 VM 缓存大小。 例如，marketplace 中的标准 Windows Server 映像大约为 127 GiB，这意味着需要的 VM 大小的缓存大于 127 GiB。 在这种情况下， [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md)的缓存大小为 86 GiB，这不够大。 Standard_DS3_v2 的缓存大小为 172 GiB，它足够大。 在这种情况下，Standard_DS3_v2 是可与此图像一起使用的 DSv2 序列中的最小大小。 Marketplace 中的基本 Linux 映像和由 `[smallsize]` 表示的 Windows Server 映像通常约为 30 GiB，可以使用大部分可用 VM 大小。

临时磁盘还要求 VM 大小支持高级存储。 通常（但不总是）在名称中有一个 `s`，如 DSv2 和 EsV3。 有关详细信息，请参阅[AZURE VM 大小](../articles/virtual-machines/linux/sizes.md)，详细了解哪些大小支持高级存储。

## <a name="powershell"></a>PowerShell

若要将临时磁盘用于 PowerShell VM 部署，请在 VM 配置中使用[AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) 。 将 `-DiffDiskSetting` 设置为 `Local` 并将 `-Caching` 为 `ReadOnly`。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

对于规模集部署，请在配置中使用[AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet。 将 `-DiffDiskSetting` 设置为 `Local` 并将 `-Caching` 为 `ReadOnly`。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

若要将临时磁盘用于 CLI VM 部署，请将[az VM create](/cli/azure/vm#az-vm-create)中的 `--ephemeral-os-disk` 参数设置为 `true` 并将 `--os-disk-caching` 参数设置为 `ReadOnly`。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

对于规模集，请使用[vmss-create](/cli/azure/vmss#az-vmss-create)相同的 `--ephemeral-os-disk true` 参数，并将 `--os-disk-caching` 参数设置为 `ReadOnly`。

## <a name="portal"></a>门户   

在 Azure 门户中，通过打开 "**磁盘**" 选项卡的 "**高级**" 部分，可以选择在部署 VM 时使用临时磁盘。对于 "**使用临时 OS 磁盘**"，请选择 **"是"** 。

![显示用于选择使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用临时磁盘的选项灰显，则可能是因为选择的 VM 大小的缓存大小不大于 OS 映像或不支持高级存储。 返回到 "**基本**信息" 页，尝试选择另一个 VM 大小。

你还可以使用门户创建具有临时 OS 磁盘的规模集。 只需确保选择足够大的缓存大小，然后在 "**使用临时 OS 磁盘**" 中选择 **"是"** ，即可选择一个 VM 大小。

![显示用于选择是否为规模集使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>规模集模板部署  
创建使用临时 OS 磁盘的规模集的过程是，将 `diffDiskSettings` 属性添加到模板中的 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 资源类型。 此外，必须将缓存策略设置为临时 OS 磁盘的 `ReadOnly`。 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM 模板部署 
你可以使用模板部署具有临时 OS 磁盘的 VM。 创建使用临时 OS 磁盘的 VM 的过程是，将 `diffDiskSettings` 属性添加到模板中的 virtualMachines/资源类型。 此外，必须将缓存策略设置为临时 OS 磁盘的 `ReadOnly`。 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>使用 REST 重置 VM 的映像
你可以使用下面所述的 REST API，并通过 Azure 门户转到 VM 的 "概述" 窗格，使用临时 OS 磁盘重置虚拟机实例的映像。 对于规模集，已通过 Powershell、CLI 和门户提供重置映像。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常见问题

**问：本地 OS 磁盘的大小是多少？**

答：我们支持平台和自定义映像，最高可达 VM 缓存大小，在这种情况下，操作系统磁盘的所有读/写将在虚拟机所在的同一节点上都是本地的。 

**问：是否可以调整临时 OS 磁盘的大小？**

答：不能，一旦预配了临时 OS 磁盘，操作系统磁盘就无法调整大小。 

**问：是否可以将托管磁盘附加到临时 VM？**

答：是的，你可以将托管数据磁盘附加到使用临时 OS 磁盘的虚拟机。 

**问：是否支持临时 OS 磁盘的所有 VM 大小？**

答：不可以，除了 B 系列、N 系列和 H 系列大小以外，所有高级存储 VM 大小均受支持（DS、ES、FS、GS 和 M）。  
 
**问：是否可以将临时 OS 磁盘应用于现有 Vm 和规模集？**

答：不能，只能在创建 VM 和规模集期间使用临时 OS 磁盘。 

**问：是否可以在规模集中混合使用暂时和普通 OS 磁盘？**

答：不能，同一规模集内不能混合使用暂时和永久性的 OS 磁盘实例。 

**问：是否可以使用 Powershell 或 CLI 创建临时 OS 磁盘？**

答：是的，可以使用 REST、模板、PowerShell 和 CLI 创建具有临时 OS 磁盘的虚拟机。

**问：临时 OS 磁盘不支持哪些功能？**

答：临时磁盘不支持：
- 捕获 VM 映像
- 磁盘快照 
- Azure 磁盘加密 
- Azure 备份
- Azure Site Recovery  
- OS 磁盘交换 
