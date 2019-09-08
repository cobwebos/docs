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
ms.openlocfilehash: c3a7fb14dbd22730d95a5aaed146b59ad790ce6b
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775826"
---
临时 OS 磁盘在本地虚拟机 (VM) 存储中创建，不保存到远程 Azure 存储。 临时 OS 磁盘适用于无状态工作负荷，在此类工作负荷中，应用程序可以容忍单个 VM 故障，但受 VM 部署时间或单个 VM 实例的映像重置的影响更大。 使用临时 OS 磁盘时，通过 OS 磁盘进行读取/写入的延迟更低，VM 重置映像速度会更快。 
 
临时磁盘 (ephemeral disk) 的重要功能包括： 
- 适用于无状态应用程序。
- 可以与市场和自定义映像配合使用。
- 能够将 VM 和规模集实例或其映像快速重置为原始启动状态。  
- 与临时磁盘一样降低延迟。 
- 临时 OS 磁盘免费，因此不需为 OS 磁盘支付存储费用。
- 它们在所有 Azure 区域中提供。 
- [共享映像库](/azure/virtual-machines/linux/shared-image-galleries)支持临时 OS 磁盘。 
 

 
持久 OS 磁盘和临时 OS 磁盘的主要区别：

|                             | 持久 OS 磁盘                          | 临时 OS 磁盘                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁盘的大小限制      | 2 TiB                                                                                        | 与 VM 大小相对应的缓存大小或 2TiB，具体取决于哪一个更小。 有关**GiB 中的缓存大小**，请[参阅 DS](../articles/virtual-machines/linux/sizes-general.md)、 [ES](../articles/virtual-machines/linux/sizes-memory.md)、 [M](../articles/virtual-machines/linux/sizes-memory.md)、 [FS](../articles/virtual-machines/linux/sizes-compute.md)和[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 支持的 VM 大小          | 全部                                                                                          | DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| 磁盘类型支持           | 托管和非托管 OS 磁盘                                                                | 仅托管 OS 磁盘                                                               |
| 区域支持              | 所有区域                                                                                  | 所有区域                              |
| 数据暂留            | 写入 OS 磁盘的 OS 磁盘数据存储在 Azure 存储中                                  | 写入 OS 磁盘的数据存储到本地 VM 存储，不保存到 Azure 存储。 |
| “已停止-解除分配”状态      | 可以先将 VM 和规模集实例停止-解除分配，然后再将其从“已停止-解除分配”状态重启 | 不能将 VM 和规模集实例停止-解除分配                                  |
| 专用 OS 磁盘支持 | 是                                                                                          | 否                                                                                 |
| OS 磁盘重设大小              | 在 VM 创建期间以及将 VM 停止-解除分配后均受支持                                | 仅在 VM 创建期间受支持                                                  |
| 将大小重设为新的 VM 大小   | 保留 OS 磁盘数据                                                                    | 删除 OS 磁盘上的数据并重新预配 OS                                      |

## <a name="size-requirements"></a>大小要求

部署 VM 和实例的映像时，其大小上限取决于 VM 缓存的大小。 例如，市场中的标准 Windows Server 映像大约为 127 GiB，这意味着所需 VM 大小的缓存大于 127 GiB。 在此示例中，[Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series) 的缓存大小为 86 GiB，不够大。 Standard_DS3_v2 缓存大小为 172 GiB，它足够大。 在此示例中，Standard_DS3_v2 是 DSv2 系列中能够用于此映像的最小大小。 市场中的基本 Linux 映像以及 `[smallsize]` 所表示的 Windows Server 映像通常为大约 30 GiB，可以使用大多数可用的 VM 大小。

临时磁盘还要求 VM 大小支持高级存储。 大小通常（但并非总是）在名称中包含 `s`，例如 DSv2 和 EsV3。 有关详细信息，请参阅 [Azure VM 大小](../articles/virtual-machines/linux/sizes.md)，其中详述了哪些大小支持高级存储。

## <a name="powershell"></a>PowerShell

若要将临时磁盘用于 PowerShell VM 部署，请在 VM 配置中使用 [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)。 将 `-DiffDiskSetting` 设置为 `Local`，将 `-Caching` 设置为 `ReadOnly`。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

对于规模集部署，请在配置中使用 [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet。 将 `-DiffDiskSetting` 设置为 `Local`，将 `-Caching` 设置为 `ReadOnly`。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

若要将临时磁盘用于 CLI VM 部署，请将 [az vm create](/cli/azure/vm#az-vm-create) 中的 `--ephemeral-os-disk` 参数设置为 `true`，将 `--os-disk-caching` 参数设置为 `ReadOnly`。

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

对于规模集，请对 [az-vmss-create](/cli/azure/vmss#az-vmss-create) 使用相同的 `--ephemeral-os-disk true` 参数，并将 `--os-disk-caching` 参数设置为 `ReadOnly`。

## <a name="portal"></a>门户   

在 Azure 门户中，通过打开 "**磁盘**" 选项卡的 "**高级**" 部分，可以选择在部署 VM 时使用临时磁盘。对于 "**使用临时 OS 磁盘**"，请选择 **"是"** 。

![显示用于选择使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用临时磁盘的选项灰显，则可能是因为选择的 VM 大小的缓存大小不大于 OS 映像或不支持高级存储。 返回到 "**基本**信息" 页，尝试选择另一个 VM 大小。

你还可以使用门户创建具有临时 OS 磁盘的规模集。 只需确保选择足够大的缓存大小，然后在 "**使用临时 OS 磁盘**" 中选择 **"是"** ，即可选择一个 VM 大小。

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
你可以使用下面所述的 REST API，并通过 Azure 门户转到 VM 的 "概述" 窗格，使用临时 OS 磁盘重置虚拟机实例的映像。 对于规模集，已经可以通过 Powershell、CLI 和门户来重置映像。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常见问题

**问：本地 OS 磁盘的大小是多少？**

答：我们支持的平台和自定义映像的大小上限取决于 VM 缓存大小，其中的 OS 磁盘的所有读取/写入操作都将在本地进行，使用与虚拟机相同的节点。 

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
