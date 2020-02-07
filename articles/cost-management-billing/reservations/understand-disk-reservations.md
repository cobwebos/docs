---
title: 了解预留折扣如何应用于 Azure 磁盘存储
description: 了解 Azure 预留磁盘折扣如何应用于高级 SSD 托管磁盘。
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902144"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>了解预留折扣如何应用于 Azure 磁盘存储

购买 Azure 磁盘预留容量后，预留折扣自动应用于与你的预留条款匹配的磁盘资源。 预留折扣仅应用于磁盘容量，磁盘快照按即用即付费率收费。

有关 Azure 磁盘预留的详细信息，请参阅[通过 Azure 磁盘预留节省成本](../../virtual-machines/linux/disks-reserved-capacity.md)。
有关 Azure 磁盘预留定价的信息，请参阅 [Azure 磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="how-the-reservation-discount-is-applied"></a>如何应用预留折扣

Azure 磁盘预留折扣是一种“不使用则失去”折扣，按小时应用于托管磁盘资源。 如果在给定的小时内没有任何托管磁盘资源满足你的预留的条款，则你会失去该小时的预留量，未使用的小时数不会结转。

删除资源时，预留折扣自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

## <a name="discount-examples"></a>折扣示例

以下示例展示了 Azure 磁盘预留折扣如何根据你的部署进行应用：

假设你在美国西部 2 区域购买了一年期 100 个 P30 磁盘（每个磁盘约 1 TiB）的预留容量。 假设此预留示例的成本为 140,100 美元。 你可以选择提前支付全部金额或者在接下来的 12 个月每月固定分期付款 11,675 美元。
就这些示例而言，假设你已注册了按月预留付款计划。 以下场景说明了当预留容量未充分使用、超额使用或分层使用时会发生的情况。

### <a name="underusing-your-capacity"></a>未充分使用容量

假设在预留期内的某个给定小时内，你只部署了 100 个 P30 磁盘预留中的 99 个 P30 高级 SSD。 剩余的 1 个 P30 在该小时内不会应用，也不会结转。

### <a name="overusing-your-capacity"></a>超额使用容量

假设在预留期内的某个给定小时内，你使用了 101 个 P30 高级 SSD。 预留折扣仅应用于 100 个 P30 磁盘，该小时剩余的 1 个 P30 磁盘按即用即付费率收费。 如果在接下来的一小时内，你的使用量减少为 100 个 P30 磁盘，则预留能涵盖所有使用量。

### <a name="tiering-your-capacity"></a>对容量进行分层

假设在预留期内的某个给定小时内，你希望使用总共 200 个 P30 高级 SSD。 在前 30 分钟内，你只使用了 100 个。 在此期间内，可以完全涵盖你的使用量，因为你预留了 100 个 P30 磁盘。 然后，如果你未继续使用前 100 个磁盘（因此你使用的是零），并且在剩余的 30 分钟内开始使用另外 100 个磁盘，则你的预留也能涵盖该使用量。

![容量未充分使用、超额使用或分层使用示例的描述](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 磁盘预留降低成本 (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [通过 Azure 磁盘预留降低成本 (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [什么是 Azure 预订？](save-compute-costs-reservations.md)