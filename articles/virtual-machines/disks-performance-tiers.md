---
title: 提高 Azure 托管磁盘性能
description: 了解托管磁盘的性能层，以及如何升级托管磁盘的性能层。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4ad0ce1aef45dc4126d3ce17c7093b885f1b8bd7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933506"
---
# <a name="performance-tiers-for-managed-disks-preview"></a> (预览版的托管磁盘的性能层) 

Azure 磁盘存储当前提供内置突发功能，以实现更高的性能，以便处理短期的意外流量。 高级 Ssd 可以灵活地提高磁盘性能，而无需增加实际磁盘大小，使你能够满足工作负荷性能需求并降低成本。 这非常适合于暂时要求更高级别的性能的事件，例如假期购物、性能测试或运行培训环境。 若要处理这些事件，可以根据需要选择较高的性能级别，并在不再需要更多性能时返回原始层。

## <a name="how-it-works"></a>工作原理

首次部署或预配磁盘时，将根据预配的磁盘大小设置该磁盘的基线性能层。 可以选择更高的性能级别以满足更高的需求，并且在不再需要该性能的情况下，你可以返回到初始基线性能层。 例如，如果预配 P10 磁盘 (128 GiB) ，则基线性能层设置为 P10 (500 IOPS 和 100 MB/秒) 。 如果不再需要更高的性能，可以更新层以匹配 P50 (7500 IOPS 和 250 MB/秒) 的性能，而不会增加磁盘大小并返回 P10。

| 磁盘大小 | 基线性能层 | 可以升级到 |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2、P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 8 GiB | P2 | P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 16 GiB | P3 | P4、P6、P10、P15、P20、P30、P40、P50 | 
| 32 GiB | P4 | P6、P10、P15、P20、P30、P40、P50 |
| 64 GiB | P6 | P10、P15、P20、P30、P40、P50 |
| 128 GiB | P10 | P15、P20、P30、P40、P50 |
| 256 GiB | P15 | P20、P30、P40、P50 |
| 512 GiB | P20 | P30、P40、P50 |
| 1 TiB | P30 | P40、P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | 无 |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | 无 |

## <a name="restrictions"></a>限制

- 目前仅支持高级 Ssd。
- 更改层前，必须从正在运行的 VM 分离磁盘。
- 使用 P60、P70 和 P80 性能层限制为 4096 GiB 或更高的磁盘。

## <a name="regional-availability"></a>区域可用性

调整托管磁盘的性能层目前仅适用于以下区域中的高级 Ssd：

- 美国中西部 
- 东 2 US 
- 西欧
- 澳大利亚东部 
- 澳大利亚东南部 
- 印度南部

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>创建/更新其层高于基线层的数据磁盘

1. 创建一个其层高于基线层的空数据磁盘，或使用示例模板[CreateUpdateDataDiskWithTier.js上](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)的示例模板更新高于基线层的磁盘层

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. 确认磁盘的层

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>创建/更新其层高于基线层的 OS 磁盘

1. 从 marketplace 映像创建 OS 磁盘，或使用示例模板[CreateUpdateOSDiskWithTier.js上](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)的示例模板更新高于基线层的 os 磁盘的层

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. 确认磁盘的层
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>后续步骤

如果必须调整磁盘大小才能利用更大的性能层，请参阅主题的文章：

- [使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](linux/expand-disks.md)
- [展开附加到 Windows 虚拟机的托管磁盘](windows/expand-os-disk.md)