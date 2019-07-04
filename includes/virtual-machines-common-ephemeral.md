---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24c2bfa4aae94642d3ed66f2cfa6e31ba1e6b19a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457235"
---
临时 OS 磁盘在本地虚拟机 (VM) 存储中创建，不保存到远程 Azure 存储。 临时 OS 磁盘适用于无状态工作负荷，在此类工作负荷中，应用程序可以容忍单个 VM 故障，但更加关注进行大规模部署所需的时间或者重置单个 VM 实例所需的时间。 它还适合将通过经典部署模型部署的应用程序移到资源管理器部署模型。 使用临时 OS 磁盘时，会观察到通过 OS 磁盘进行读取/写入时的延迟更低，VM 重置映像速度会更快。 另外，临时 OS 磁盘免费，因此不需为 OS 磁盘支付存储费用。 
 
临时磁盘 (ephemeral disk) 的重要功能包括： 
- 可以与市场映像和自定义映像配合使用。
- 部署 VM 映像时，其大小上限取决于 VM 缓存的大小。
- 能够将其 VM 或 VM 映像快速重置为原始启动状态。  
- 与临时磁盘 (temporary disk) 一样降低运行时延迟。 
- OS 磁盘免费。 
 
 
持久 OS 磁盘和临时 OS 磁盘的主要区别：

|                             | 持久 OS 磁盘                          | 临时 OS 磁盘                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁盘的大小限制      | 2 TiB                                                                                        | 与 VM 大小相对应的缓存大小或 2TiB，具体取决于哪一个更小 - [DS](../articles/virtual-machines/linux/sizes-general.md)、[ES](../articles/virtual-machines/linux/sizes-memory.md)、[M](../articles/virtual-machines/linux/sizes-memory.md)、[FS](../articles/virtual-machines/linux/sizes-compute.md) 和 [GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| 支持的 VM 大小          | 全部                                                                                          | DSv1、DSv2、DSv3、Esv3、Fs、FsV2、GS、M                                               |
| 磁盘类型支持           | 托管和非托管 OS 磁盘                                                                | 仅托管 OS 磁盘                                                               |
| 区域支持              | 所有区域                                                                                  | 所有区域                              |
| 数据暂留            | 写入 OS 磁盘的 OS 磁盘数据存储在 Azure 存储中                                  | 写入 OS 磁盘的数据存储到本地 VM 存储，不保存到 Azure 存储。 |
| “已停止-解除分配”状态      | 可以先将 VM 和规模集实例停止-解除分配，然后再将其从“已停止-解除分配”状态重启 | 不能将 VM 和规模集实例停止-解除分配                                  |
| 专用 OS 磁盘支持 | 是                                                                                          | 否                                                                                 |
| OS 磁盘重设大小              | 在 VM 创建期间以及将 VM 停止-解除分配后均受支持                                | 仅在 VM 创建期间受支持                                                  |
| 将大小重设为新的 VM 大小   | 保留 OS 磁盘数据                                                                    | 删除 OS 磁盘上的数据并重新预配 OS                                      |

## <a name="scale-set-deployment"></a>规模集部署  
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

## <a name="vm-deployment"></a>VM 部署 
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

答：对于预览版，我们支持的平台和/或映像的大小上限取决于 VM 缓存大小，其中的 OS 磁盘的所有读取/写入操作都将在本地进行，使用与虚拟机相同的节点。 

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
