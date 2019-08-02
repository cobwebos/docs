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
ms.openlocfilehash: 0e59a28ce1fb3c562bf76420a5e62e347230e964
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669552"
---
在本地虚拟机 (VM) 存储上创建暂时的 OS 磁盘, 而不会将其保存到远程 Azure 存储。 临时 OS 磁盘适用于无状态工作负荷, 其中应用程序可以容忍单个 VM 故障, 但会受到 VM 部署时间或重新映像单个 VM 实例的影响。 通过临时 OS 磁盘, 可以更低的读取/写入延迟到 OS 磁盘和更快的 VM 重置。 
 
临时磁盘 (ephemeral disk) 的重要功能包括： 
- 适用于无状态应用程序。
- 它们可与 Marketplace 映像和自定义映像一起使用。
- 能够快速将 Vm 和规模集实例重置或重新映像到原始启动状态。  
- 延迟较低, 与临时磁盘类似。 
- 临时 OS 磁盘是免费的, 操作系统磁盘不会产生存储费用。
- 它们在所有 Azure 区域中均可用。 
- [共享映像库](/azure/virtual-machines/linux/shared-image-galleries)支持临时 OS 磁盘。 
 

 
持久 OS 磁盘和临时 OS 磁盘的主要区别：

|                             | 持久 OS 磁盘                          | 临时 OS 磁盘                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁盘的大小限制      | 2 TiB                                                                                        | VM 大小或2TiB 的缓存大小, 以较小者为准。 有关**GiB 中的缓存大小**, 请[参阅 DS](../articles/virtual-machines/linux/sizes-general.md)、 [ES](../articles/virtual-machines/linux/sizes-memory.md)、 [M](../articles/virtual-machines/linux/sizes-memory.md)、 [FS](../articles/virtual-machines/linux/sizes-compute.md)和[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 支持的 VM 大小          | 全部                                                                                          | DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| 磁盘类型支持           | 托管和非托管 OS 磁盘                                                                | 仅托管 OS 磁盘                                                               |
| 区域支持              | 所有区域                                                                                  | 所有区域                              |
| 数据暂留            | 写入 OS 磁盘的 OS 磁盘数据存储在 Azure 存储中                                  | 写入 OS 磁盘的数据存储到本地 VM 存储，不保存到 Azure 存储。 |
| “已停止-解除分配”状态      | 可以先将 VM 和规模集实例停止-解除分配，然后再将其从“已停止-解除分配”状态重启 | 不能将 VM 和规模集实例停止-解除分配                                  |
| 专用 OS 磁盘支持 | 是                                                                                          | 否                                                                                 |
| OS 磁盘重设大小              | 在 VM 创建期间以及将 VM 停止-解除分配后均受支持                                | 仅在 VM 创建期间受支持                                                  |
| 将大小重设为新的 VM 大小   | 保留 OS 磁盘数据                                                                    | 删除 OS 磁盘上的数据并重新预配 OS                                      |

## <a name="size-requirements"></a>大小要求

你可以部署 VM 和实例映像, 最大大小为 VM 缓存大小。 例如, marketplace 中的标准 Windows Server 映像大约为 127 GiB, 这意味着需要的 VM 大小的缓存大于 127 GiB。 在这种情况下, [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series)的缓存大小为 86 GiB, 这不够大。 Standard_DS3_v2 缓存大小为 172 GiB, 它足够大。 在这种情况下, Standard_DS3_v2 是可与此图像一起使用的 DSv2 系列中的最小大小。 Marketplace 中的基本 Linux 映像和由表示的`[smallsize]` Windows Server 映像往往约为 30 GiB, 可以使用大多数可用的 VM 大小。

临时磁盘还要求 VM 大小支持高级存储。 通常 (但不总是) `s`在名称中包含 (如 DSv2 和 EsV3)。 有关详细信息, 请参阅[AZURE VM 大小](../articles/virtual-machines/linux/sizes.md), 详细了解哪些大小支持高级存储。

## <a name="powershell"></a>PowerShell

若要将临时磁盘用于 PowerShell VM 部署, 请在 VM 配置中使用[AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) 。 `Local`将设置`-DiffDiskSetting`为和。`-Caching` `ReadOnly`     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

对于规模集部署, 请在配置中使用[AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet。 `Local`将设置`-DiffDiskSetting`为和。`-Caching` `ReadOnly`


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

`--ephemeral-os-disk`若要将临时磁盘用于 CLI VM 部署, 请将[az](/cli/azure/vm#az-vm-create) `true` VM create 中的参数设置为, `--os-disk-caching`并将`ReadOnly`参数设置为。

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

对于规模集, 可对[az-vmss](/cli/azure/vmss#az-vmss-create)使用同一个`--os-disk-caching` `--ephemeral-os-disk true`参数, 并将参数设置为`ReadOnly`。

## <a name="portal"></a>门户   

在 Azure 门户中, 通过打开 "**磁盘**" 选项卡的 "**高级**" 部分, 可以选择在部署 VM 时使用临时磁盘。对于 "**使用临时 OS 磁盘**", 请选择 **"是"** 。

![显示用于选择使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用临时磁盘的选项灰显, 则可能是因为选择的 VM 大小的缓存大小不大于 OS 映像或不支持高级存储。 返回到 "**基本**信息" 页, 尝试选择另一个 VM 大小。

你还可以使用门户创建具有临时 OS 磁盘的规模集。 只需确保选择足够大的缓存大小, 然后在 "**使用临时 OS 磁盘**" 中选择 **"是"** , 即可选择一个 VM 大小。

![显示用于选择是否为规模集使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>规模集模板部署  
创建一个使用临时 OS 磁盘的规模集时，其过程很简单，就是将 `diffDiskSettings` 属性添加到模板中的 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 资源类型。 另外，对于临时 OS 磁盘，必须将缓存策略设置为 `ReadOnly`。 


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
可以通过模板部署使用临时 OS 磁盘的 VM。 创建一个使用临时 OS 磁盘的 VM 时，其过程很简单，就是将 `diffDiskSettings` 属性添加到模板中的 Microsoft.Compute/virtualMachines 资源类型。 另外，对于临时 OS 磁盘，必须将缓存策略设置为 `ReadOnly`。 

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


## <a name="reimage-a-vm-using-rest"></a>使用 REST 重置 VM 映像
目前，若要重置使用临时 OS 磁盘的虚拟机实例的映像，唯一方法是使用 REST API。 对于规模集，已经可以通过 Powershell、CLI 和门户来重置映像。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常见问题

**问：本地 OS 磁盘的大小是多少？**

答：我们支持平台和自定义映像, 最高可达 VM 缓存大小, 在此情况下, OS 磁盘的所有读/写操作都将在虚拟机所在的同一节点上本地运行。 

**问：可以重设临时 OS 磁盘的大小吗？**

答：不可以。预配临时 OS 磁盘以后，就不能重设其大小。 

**问：是否可以将托管磁盘附加到临时 VM？**

答：是的，可以将托管数据磁盘附加到使用临时 OS 磁盘的 VM。 

**问：临时 OS 磁盘是否支持所有 VM 大小？**

答：否。支持除 B 系列、N 系列和 H 系列大小之外的所有高级存储 VM 大小（DS、ES、FS、GS 和 M）。  
 
**问：是否可以对现有的 VM 和规模集应用临时 OS 磁盘？**

答：否。只能在 VM 和规模集创建期间使用临时 OS 磁盘。 

**问：是否可以在规模集中混合使用临时 OS 磁盘和常规 OS 磁盘？**

答：否。不能在同一规模集中混合使用临时 OS 磁盘实例和持久 OS 磁盘实例。 

**问：是否可以通过 Powershell 或 CLI 创建临时 OS 磁盘？**

答：是的，可以通过 REST、模板、PowerShell 或 CLI 创建使用临时 OS 磁盘的 VM。

**问：临时 OS 磁盘不支持哪些功能？**

答：临时磁盘不支持：
- 捕获 VM 映像
- 磁盘快照 
- Azure 磁盘加密 
- Azure 备份
- Azure Site Recovery  
- OS 磁盘交换 
