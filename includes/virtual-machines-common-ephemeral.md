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
ms.openlocfilehash: 4e31560126919e4c61b176a6eaa62ee7f9b4a624
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112088"
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

部署 VM 和实例的映像时，其大小上限取决于 VM 缓存的大小。 例如，市场中的标准 Windows Server 映像大约为 127 GiB，这意味着所需 VM 大小的缓存大于 127 GiB。 在此示例中，[Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) 的缓存大小为 86 GiB，不够大。 Standard_DS3_v2 的缓存大小为 172 GiB，足够大。 在此示例中，Standard_DS3_v2 是 DSv2 系列中能够用于此映像的最小大小。 市场中的基本 Linux 映像以及 `[smallsize]` 所表示的 Windows Server 映像通常为大约 30 GiB，可以使用大多数可用的 VM 大小。

临时磁盘还要求 VM 大小支持高级存储。 大小通常（但并非总是）在名称中包含 `s`，例如 DSv2 和 EsV3。 有关详细信息，请参阅 [Azure VM 大小](../articles/virtual-machines/linux/sizes.md)，其中详述了哪些大小支持高级存储。

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>预览-暂时的 OS 磁盘现在可以存储在临时磁盘上
暂时的 OS 磁盘除了 VM 缓存外，还可以存储在 VM temp/资源磁盘上。 因此，现在你可以使用不具有缓存或缓存不足的虚拟机磁盘，但具有用于存储临时 OS 磁盘的临时/资源磁盘，例如 Dav3、Dav4、Eav4 和 Eav3。 如果 VM 具有足够的缓存和临时空间，你现在还可以使用名为[DiffDiskPlacement](https://docs.microsoft.com/rest/api/compute/virtualmachines/list#diffdiskplacement)的新属性指定要存储临时 OS 磁盘的位置。 此功能目前以预览版提供。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 若要开始，请[请求访问](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)。

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

在 Azure 门户中，通过打开 "**磁盘**" 选项卡的 "**高级**" 部分，可以选择在部署 VM 时使用临时磁盘。对于 "**使用临时 OS 磁盘**"，请选择 **"是"**。

![显示单选按钮的屏幕截图，该按钮选中后即可使用临时 OS 磁盘](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用临时磁盘的选项灰显，则可能是因为选择的 VM 大小没有大于 OS 映像的缓存大小，或者不支持高级存储。 返回到“基本信息”页，尝试选择另一 VM 大小。****

也可通过门户创建使用临时 OS 磁盘的规模集。 只需确保所选 VM 大小具有足够大的缓存大小，然后在“使用临时 OS 磁盘”中选择“是”即可。********

![显示单选按钮的屏幕截图，该按钮选中后即可使用规模集的临时 OS 磁盘](./media/virtual-machines-common-ephemeral/scale-set.png)

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
可以通过 REST API 重置使用临时 OS 磁盘的虚拟机实例的映像，详见下面的说明，只需通过 Azure 门户转到 VM 的“概览”窗格即可。 对于规模集，已经可以通过 Powershell、CLI 和门户来重置映像。

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

答：不可以，支持大多数高级存储 VM 大小（DS、ES、FS、GS、M 等）。 若要了解特定 VM 大小是否支持临时 OS 磁盘，可以：

调用 `Get-AzComputeResourceSku` PowerShell cmdlet
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
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
