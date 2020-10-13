---
title: 更改 Azure 托管磁盘的性能
description: 了解托管磁盘的性能层，并了解如何更改现有托管磁盘的性能层。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: efbe8bc24b430716da46601ed073300e4c79cca7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743720"
---
# <a name="performance-tiers-for-managed-disks-preview"></a> (预览版的托管磁盘的性能层) 

Azure 磁盘存储当前提供内置突发功能，以提供更高的性能来处理短期的意外流量。 高级 Ssd 可以灵活地提高磁盘性能，而无需增加实际磁盘大小。 此功能可让你满足工作负荷性能需求并降低成本。 

> [!NOTE]
> 此功能目前处于预览状态。 

此功能非常适合于暂时要求一致的更高性能级别的事件，例如假期购物、性能测试或运行培训环境。 若要处理这些事件，你可以根据需要使用更高的性能层。 当你不再需要更多性能时，可以返回原始层。

## <a name="how-it-works"></a>工作原理

首次部署或预配磁盘时，将根据预配的磁盘大小设置该磁盘的基线性能层。 你可以使用更高的性能级别以满足更高的需求。 当你不再需要该性能级别时，可以返回到初始基线性能层。

当你的层发生变化时，你的帐单会发生变化。 例如，如果预配 P10 磁盘 (128 GiB) ，则基线性能层设置为 P10 (500 IOPS 和 100 MBps) 。 将按 P10 费率收费。 你可以升级该层，使其与 P50 (7500 IOPS 和 250 MBps) 的性能保持一致，而不会增加磁盘大小。 在升级期间，将按 P50 费率收费。 如果不再需要更高的性能，可以返回到 P10 层。 磁盘将再次按 P10 费率收费。

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

- 此功能目前仅适用于高级 Ssd。
- 必须先从正在运行的 VM 中分离磁盘，然后才能更改磁盘层。
- 使用 P60、P70 和 P80 性能层限制为 4096 GiB 或更高的磁盘。
- 磁盘的性能层每24小时只能更改一次。

## <a name="regional-availability"></a>区域可用性

目前仅在美国西部地区的高级 Ssd 上提供了调整托管磁盘性能层的功能。 

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

如果需要调整磁盘大小以利用更高的性能层，请参阅以下文章：

- [使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](linux/expand-disks.md)
- [展开附加到 Windows 虚拟机的托管磁盘](windows/expand-os-disk.md)
