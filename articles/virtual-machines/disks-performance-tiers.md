---
title: 提高 Azure 托管磁盘性能
description: 了解托管磁盘的性能层，以及如何升级托管磁盘的性能层。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 3d6b243ab517f3663f779d01569acf3d46ad8411
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328116"
---
# <a name="performance-tiers-for-managed-disks-preview"></a> (预览版的托管磁盘的性能层) 

Azure 磁盘存储当前提供内置突发功能，以实现更高的性能，以便处理短期的意外流量。 高级 Ssd 可以灵活地提高磁盘性能，而无需增加实际磁盘大小，使你能够满足工作负荷性能需求并降低成本，此功能目前处于预览阶段。 这非常适合于暂时要求更高级别的性能的事件，例如假期购物、性能测试或运行培训环境。 若要处理这些事件，可以根据需要选择较高的性能级别，并在不再需要更多性能时返回原始层。

## <a name="how-it-works"></a>工作原理

首次部署或预配磁盘时，将根据预配的磁盘大小设置该磁盘的基线性能层。 可以选择更高的性能级别以满足更高的需求，并且在不再需要该性能的情况下，你可以返回到初始基线性能层。

当你的层发生变化时，你的帐单会发生变化。 例如，如果你预配了 P10 disk (128 GiB) ，则基线性能层将设置为 P10 (500 IOPS 和 100 MB/秒) ，并且将按 P10 费率计费。 可以更新层以匹配 P50 (7500 IOPS 和 250 MB/秒的性能) 不增加磁盘大小，在这段时间内将按 P50 费率计费。 如果不再需要更高的性能，可以返回到 P10 层，磁盘将再次按 P10 费率计费。

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

有关计费信息，请参阅 [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="restrictions"></a>限制

- 目前仅支持高级 Ssd。
- 更改层前，必须从正在运行的 VM 分离磁盘。
- 使用 P60、P70 和 P80 性能层限制为 4096 GiB 或更高的磁盘。
- 磁盘性能层每24小时只能更改一次。

## <a name="regional-availability"></a>区域可用性

调整托管磁盘的性能层目前仅适用于以下区域中的高级 Ssd：

- 美国中西部 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>使用比基线层高的层创建空数据磁盘

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>使用比 Azure Marketplace 映像中的基线层高的层创建 OS 磁盘

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>更新磁盘层

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>显示磁盘的层

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>后续步骤

如果必须调整磁盘大小才能利用更大的性能层，请参阅主题的文章：

- [使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](linux/expand-disks.md)
- [展开附加到 Windows 虚拟机的托管磁盘](windows/expand-os-disk.md)
