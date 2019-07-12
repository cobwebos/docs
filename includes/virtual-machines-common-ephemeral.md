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
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805875"
---
临时 OS 磁盘是在本地虚拟机 (VM) 存储中创建并不会保存到远程 Azure 存储。 临时 OS 磁盘工作也为无状态工作负载，其中应用程序都容许的单个 VM 失败，但更影响的 VM 部署时间或重置映像的单个 VM 实例。 使用临时 OS 磁盘后，您可以获得较低的读/写延迟，OS 磁盘和更快的 VM 重置映像。 
 
临时磁盘 (ephemeral disk) 的重要功能包括： 
- 适用于无状态应用程序。
- 它们可以用于 Marketplace 和自定义映像。
- 可以快速重置或重置映像 Vm 和规模集实例到原始的启动状态。  
- 较低的滞后时间，类似于临时磁盘。 
- 临时 OS 磁盘可免费使用，则会产生的 OS 磁盘存储免费。
- 它们在所有 Azure 区域均可用。 
- 通过支持临时 OS 磁盘[共享映像库](/azure/virtual-machines/linux/shared-image-galleries)。 
 

 
持久 OS 磁盘和临时 OS 磁盘的主要区别：

|                             | 持久 OS 磁盘                          | 临时 OS 磁盘                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁盘的大小限制      | 2 TiB                                                                                        | 缓存大小的 VM 大小或 2TiB，两者中较小。 有关**缓存以 GiB 为单位的大小**，请参阅[DS](../articles/virtual-machines/linux/sizes-general.md)， [ES](../articles/virtual-machines/linux/sizes-memory.md)， [M](../articles/virtual-machines/linux/sizes-memory.md)， [FS](../articles/virtual-machines/linux/sizes-compute.md)，和[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 支持的 VM 大小          | All                                                                                          | DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| 磁盘类型支持           | 托管和非托管 OS 磁盘                                                                | 仅托管 OS 磁盘                                                               |
| 区域支持              | 所有区域                                                                                  | 所有区域                              |
| 数据暂留            | 写入 OS 磁盘的 OS 磁盘数据存储在 Azure 存储中                                  | 写入 OS 磁盘的数据存储到本地 VM 存储，不保存到 Azure 存储。 |
| “已停止-解除分配”状态      | 可以先将 VM 和规模集实例停止-解除分配，然后再将其从“已停止-解除分配”状态重启 | 不能将 VM 和规模集实例停止-解除分配                                  |
| 专用 OS 磁盘支持 | 是                                                                                          | 否                                                                                 |
| OS 磁盘重设大小              | 在 VM 创建期间以及将 VM 停止-解除分配后均受支持                                | 仅在 VM 创建期间受支持                                                  |
| 将大小重设为新的 VM 大小   | 保留 OS 磁盘数据                                                                    | 删除 OS 磁盘上的数据并重新预配 OS                                      |

## <a name="size-requirements"></a>大小要求

你可以部署 VM 和实例的映像，最多 VM 缓存的大小。 例如，在 marketplace 中的标准 Windows Server 映像是约 127 GiB，这意味着你需要具有大于 127 GiB 的缓存的 VM 大小。 在这种情况下， [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series)有的缓存大小为 86 GiB，这是不足够大。 Standard_ds2_v2，并具有 172 GiB 的缓存大小足够大。 在这种情况下，Standard_DS3_v2 是可以使用与此图像的 DSv2 系列中的最小大小。 由表示的应用商店和 Windows Server 映像中的基本 Linux 映像`[smallsize]`往往是大约 30 GiB 和可以使用大部分可用的 VM 大小。

临时磁盘也需要该 VM 大小支持高级存储。 大小通常 （但不是总是） 有`s`中的名称，如 DSv2 和 esv3 系列。 有关详细信息，请参阅[Azure VM 大小](../articles/virtual-machines/linux/sizes.md)有关围绕其大小支持高级存储的详细信息。

## <a name="powershell"></a>PowerShell

若要为 PowerShell VM 部署中使用临时磁盘，请使用[集 AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) VM 配置中。 设置`-DiffDiskSetting`到`Local`并`-Caching`到`ReadOnly`。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

有关规模集部署，使用[集 AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile)在配置中的 cmdlet。 设置`-DiffDiskSetting`到`Local`并`-Caching`到`ReadOnly`。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

若要为 CLI VM 部署中使用临时磁盘，将`--ephemeral-os-disk`中的参数[az vm 创建](/cli/azure/vm#az-vm-create)到`true`并`--os-disk-caching`参数`ReadOnly`。

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

对于规模集，使用相同`--ephemeral-os-disk true`参数[az vmss 创建](/cli/azure/vmss#az-vmss-create)并设置`--os-disk-caching`参数`ReadOnly`。

## <a name="portal"></a>门户   

在 Azure 门户中，您可以选择通过打开部署 VM 时使用临时磁盘**高级**一部分**磁盘**选项卡。有关**使用临时 OS 磁盘**选择**是**。

![显示用于选择要使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用临时磁盘的选项灰显，您可能选择不具有大于 OS 映像的缓存大小或不支持高级存储的 VM 大小。 返回到**基础知识**页上，并尝试选择另一个 VM 大小。

此外可以使用临时 OS 磁盘使用门户创建规模集。 只需确保所选的 VM 大小足够大，缓存大小，然后在**使用临时 OS 磁盘**选择**是**。

![显示用于选择要为规模集使用临时 OS 磁盘的单选按钮的屏幕截图](./media/virtual-machines-common-ephemeral/scale-set.png)

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

答：我们支持的平台和自定义映像，最大 VM 缓存大小，将在其中放置虚拟机作为在同一节点上本地所有读/写到 OS 磁盘。 

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
