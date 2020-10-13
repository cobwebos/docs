---
title: 虚拟机大小灵活性-Azure 保留 VM 实例
description: 了解购买虚拟机预留实例时哪些大小系列有预留折扣。
author: manish-shukla01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: manshuk
ms.openlocfilehash: 381023a0650b078a74c01ff7b698aebacb256147
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855511"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>虚拟机预留实例的虚拟机大小灵活性

购买保留 VM 实例时，可以选择优化实例大小的灵活性或容量优先级。 有关设置或更改保留 VM 实例的优化设置的详细信息，请参阅 [更改保留 vm 实例的优化设置](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。

使用为实例大小灵活性优化的保留虚拟机实例，你购买的预订可应用于虚拟机 (Vm) 大小在同一实例大小的灵活性组中。 例如，如果购买了 DSv2 系列中列出的 VM 大小的保留，如 Standard_DS5_v2，则预订折扣可以应用于同一实例大小灵活性组中列出的其他四种大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但预订折扣不适用于不同实例大小灵活性组中列出的 Vm 大小，如 DSv2 系列中的 Sku 高内存： Standard_DS11_v2、Standard_DS12_v2 等。

在 "实例大小" 灵活性组内，预订折扣适用的 Vm 数取决于你在购买预订时选择的 VM 大小。 也取决于运行的 VM 的大小。 比值列比较该实例大小灵活性组中每个 VM 大小的相对占用量。 请使用比率值来计算如何将预留折扣应用到运行的 VM。

## <a name="examples"></a>示例

以下示例使用 DSv2 系列表中的大小和比率。

你购买一个大小为 Standard_DS4_v2 的虚拟机预留实例，其中的比率或相对于该系列中其他大小的占用量为 8。

- 方案 1：运行 8 个比率为 1 的 Standard_DS1_v2 大小的 VM。 预留折扣适用于这所有 8 个 VM。
- 方案 2：运行 2 个 Standard_DS2_v2 大小的 VM，每个的比率为 2。 另外，运行一个比率为 4 的 Standard_DS3_v2 大小的 VM。 总占用量为 2+2+4=8。 因此，预留折扣适用于这所有 3 个 VM。
- 方案 3：运行 1 个比率为 16 的 Standard_DS5_v2 大小的 VM。 预留折扣适用于该 VM 的半数计算费用。

以下部分介绍了在购买优化的可以灵活调整实例大小的虚拟机预留实例时，同一大小系列组中有哪些大小。

## <a name="instance-size-flexibility-ratio-for-vms"></a>Vm 的实例大小灵活性比率 

下面的 CSV 具有实例大小灵活性组、ArmSkuName 和比率。  

[实例大小灵活性比率](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

我们会保持文件 URL 和架构是固定的，以便可以通过编程方式使用此文件。 此外，还可以通过 API 获取数据。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [什么是 Azure 保留](../cost-management-billing/reservations/save-compute-costs-reservations.md)。
