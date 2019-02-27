---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 972a538fab8a2aa84f6a12df48422abb40baac82
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443302"
---
# <a name="enabling-azure-ultra-ssd"></a>启用 Azure 超级 SSD

Azure 超级 SSD 为 Azure IaaS VM 提供高吞吐量、高 IOPS 和一贯低延迟的磁盘存储。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超级 SSD 的其他优势包括能够动态改变工作负荷以及磁盘的性能，而无需重启虚拟机。 超级 SSD 适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。

目前，超级 SSD 为预览版，因此必须[注册](https://aka.ms/UltraSSDPreviewSignUp)预览版才能使用它们。

获得批准后，请运行以下任一命令以确定要将超级磁盘部署到美国东部 2 中的哪个区域：

PowerShell：`Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI：`az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

响应将类似于以下形式，其中的 X 表示要用于在美国东部 2 中进行部署的区域。 X 可能是1、2 或 3。

|ResourceType  |Name  |位置  |区域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

如果命令没有返回任何响应，这表示你对使用此功能的注册还处于等待处理状态或者尚未获得批准。

在知道了要部署到哪个区域后，请按照本文中的部署步骤来部署第一个具有超级 SSD 的 VM。

## <a name="deploying-an-ultra-ssd"></a>部署超级 SSD

首先，确定要部署的 VM 大小。 在此预览版中，仅支持 DsV3 和 EsV3 VM 系列。 有关这些 VM 大小的其他详细信息，请参阅此[博客](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)上的第二个表格。
此外，请参阅示例[创建具有多个超级 SSD 的 VM](https://aka.ms/UltraSSDTemplate) 以了解如何创建具有多个超级 SSD 的 VM。

下面介绍了新的/修改的资源管理器模板更改：必须将 `Microsoft.Compute/virtualMachines` 和 `Microsoft.Compute/Disks` 的 apiVersion 设置为 `2018-06-01`（或之后）。

指定磁盘 Sku UltraSSD_LRS、磁盘容量、IOPS 和吞吐量 (MBps) 来创建超级磁盘。 以下是创建 1,024 GiB（GiB = 2^30 字节）、80,000 IOPS 和 1,200 MBps（MBps = 每秒 10^6 字节）的磁盘的示例：

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

在 VM 的属性上添加一个额外功能来指示其超级功能已启用（请参阅[示例](https://aka.ms/UltraSSDTemplate)以查看完整的资源管理器模板）：

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

在预配 VM 后，可以对数据磁盘进行分区和格式设置并为工作负荷配置这些磁盘。

## <a name="additional-ultra-ssd-scenarios"></a>其他超级 SSD 方案

- 在 VM 创建期间，也可以隐式创建超级 SSD。 但是，这些磁盘将收到 IOPS (500) 和吞吐量（8 MiB/秒）的默认值。
- 其他超级 SSD 可以附加到兼容的 VM 上。
- 超级 SSD 支持在运行时调整磁盘性能属性（IOPS 和吞吐量），而无需从虚拟机拆离磁盘。 在对磁盘上发出磁盘性能调整大小操作后，更改实际上可能最多需要一个小时才能生效。
- 增加磁盘容量需要释放虚拟机。

## <a name="next-steps"></a>后续步骤

如果想尝试新的磁盘类型并且尚未注册预览版，可以[通过此调查请求访问](https://aka.ms/UltraSSDPreviewSignUp)。
