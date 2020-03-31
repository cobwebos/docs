---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471443"
---
购买预留 VM 实例时，可以选择优化实例大小灵活性或容量优先级。 有关设置或更改保留 VM 实例的优化设置的详细信息，请参阅[更改保留 VM 实例的优化设置](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)。

通过针对实例大小灵活性进行优化的预留虚拟机实例，您购买的预留可以应用于相同实例大小灵活地组中的虚拟机 （VM） 大小。 例如，如果您为 DSv2 系列中列出的 VM 大小（如Standard_DS5_v2）购买预留，则预留折扣可以应用于同一实例大小灵活地组中列出的其他四种大小：

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

但是，预订折扣不适用于在不同实例大小灵活地组中列出的 VM 大小，如 DSv2 系列高内存中的 SKU：Standard_DS11_v2、Standard_DS12_v2等。

在实例大小灵活性组中，预留折扣适用的 VM 数量取决于您在购买预留时选择的 VM 大小。 也取决于运行的 VM 的大小。 比率列比较该实例大小灵活性组中每个 VM 大小的相对占用空间。 请使用比率值来计算如何将预留折扣应用到运行的 VM。

## <a name="examples"></a>示例

以下示例使用 DSv2 系列表中的大小和比率。

你购买一个大小为 Standard_DS4_v2 的虚拟机预留实例，其中的比率或相对于该系列中其他大小的占用量为 8。

- 方案 1：运行 8 个比率为 1 的 Standard_DS1_v2 大小的 VM。 预留折扣适用于这所有 8 个 VM。
- 方案 2：运行 2 个 Standard_DS2_v2 大小的 VM，每个的比率为 2。 另外，运行一个比率为 4 的 Standard_DS3_v2 大小的 VM。 总占用量为 2+2+4=8。 因此，预留折扣适用于这所有 3 个 VM。
- 方案 3：运行 1 个比率为 16 的 Standard_DS5_v2 大小的 VM。 预留折扣适用于该 VM 的半数计算费用。

以下部分介绍了在购买优化的可以灵活调整实例大小的虚拟机预留实例时，同一大小系列组中有哪些大小。

## <a name="instance-size-flexibility-ratio-for-vms"></a>VM 的实例大小灵活性比 

下面的 CSV 具有实例大小灵活性组、ArmSkuName 和比率。  

[实例大小灵活性比](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

我们将保持文件 URL 和架构固定，以便您可以以编程方式使用此文件。 数据也将很快通过 API 提供。
