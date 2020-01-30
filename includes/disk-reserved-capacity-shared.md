---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847129"
---
使用保留容量保存到高级固态硬盘（SSD）的使用情况，并将保留的虚拟机实例与保留的虚拟机实例相结合，可以降低 VM 总成本。 预订折扣将自动应用于所选预订范围内的匹配磁盘，无需将预订分配给托管磁盘以获取折扣。 每小时按磁盘使用情况应用折扣，任何未使用的保留容量不会接管。 托管磁盘保留折扣不适用于非托管磁盘、超磁盘或页 blob 消耗。

## <a name="determine-your-storage-needs"></a>确定存储需求

在购买预订之前，你应该确定存储需求。 目前，磁盘保留仅适用于选择高级 SSD Sku。 高级 SSD 的 SKU 决定磁盘的大小和性能。 在确定存储需求时，我们不建议考虑使用磁盘作为总容量，不能将预留用于更大的磁盘（如 P40），并使用它来支付两个较小的磁盘（P30）。 购买预订时，你只购买每个 SKU 的磁盘总数。

磁盘保留按磁盘 SKU 进行，因此保留消耗基于磁盘 Sku 的单位，而不是所提供的大小。 例如，如果你保留了 1 P40 2 TiB 预配容量，但仅分配了2个 P30 磁盘，则这两个 P30 消耗将不考虑 P40 预留，你会按现用现付费率付费。



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>购买注意事项

建议在考虑磁盘保留购买时采用以下最佳做法：

- 分析你的使用情况信息，以帮助确定你应购买哪些预订。 请确保正在跟踪磁盘 Sku 中的使用情况，而不是预配或使用的磁盘容量。 
- 检查磁盘保留，以及 VM 预留。 强烈建议为 VM 和磁盘使用预留，以实现最大限度的节省。 你可以从确定正确的 VM 预留开始，然后相应地评估磁盘预留。 通常，每个工作负荷都有一个标准配置，例如，SQL server 可能有两个 P40 数据磁盘和一个 P30 OS 磁盘。 这种模式可帮助您确定您可能购买的预留量。 这种方法可以简化评估过程，还可以确保在订阅、区域和其他方面，同时为 VM 和磁盘提供协调计划。 

## <a name="purchase-restrictions"></a>购买限制

预订折扣当前不适用于以下磁盘：
- 非托管磁盘/页 blob
- 标准 SSD 或标准 HDD
- 高级 SSD Sku 小于 P30 –预订不适用于 P1/P2/P3/P4/P6/P10/P15/P20 高级 SSD Sku。
- 云预订不适用于 Azure Gov、德国或中国地区的购买。
- 容量限制-在极少数情况下，Azure 会限制磁盘 Sku 子集的新保留购买，因为区域中的容量不足。

## <a name="buy-a-disk-reservation"></a>购买磁盘保留

可以通过[Azure 门户](https://portal.azure.com/)购买 Azure 磁盘预留。 您可以提前支付预订费用，也可以按月支付。 有关每月支付购买的详细信息，请参阅[每月支付的购买预留](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。

请按照以下步骤购买预留容量：

1. 导航到 Azure 门户中的 "[采购预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)" 边栏选项卡。
1. 选择 " **Azure 托管磁盘**" 以购买预订。

    ![disks-reserved-purchase-reservation .png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. 填写必填字段，如下表所示：

   |字段  |Description  |
   |---------|---------|
   |**范围**   |  指示可以使用与预订关联的计费权益的订阅数。 它还控制将预留应用于特定订阅的方式。 <br/><br/> 如果选择 "**共享**"，则预订折扣将应用到计费上下文中任何订阅的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包含由帐户管理员创建的即用即付费率的所有订阅。  <br/><br/>  如果选择 "**单个订阅**"，则预订折扣将应用到所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择 "**单个资源组**"，则预订折扣将应用到所选订阅中的 Azure 存储容量和该订阅内的所选资源组。 <br/><br/> 你可以在购买预订后更改保留范围。  |
   |**订阅**  | 用于支付 Azure 存储预留的订阅。 所选订阅的付款方式用于对成本进行收费。 订阅必须是以下类型之一： <br/><br/>  企业协议（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p）：对于企业订阅，将从注册的货币承诺余额中扣除费用，或将其收费为超额。 <br/><br/> 使用即用即付费率的单个订阅（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0003p 或 BC-OP-NT-AZR-0023P）：对于使用即用即付费率的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。    |
   | **磁盘** | 要创建的 SKU。 |
   | **区域** | 预订有效的区域。 |
   | **计费频率** | 指示帐户按预订计费的频率。 选项包括 "*每月*" 或 "*提前*"。 |

    ![premium-ssd-reserved-purchase-selection .png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 选择预订参数后，Azure 门户会显示成本。 该门户还显示 "即用即付" 计费的折扣百分比。 选择 "**下一步**" 以转到 "**购买预订**" 边栏选项卡。

1. 在 "**购买预订**" 窗格中，可以命名预订，并选择要进行的预订的总数量。 预订数映射到磁盘数。 例如，如果你想要保留一百个磁盘，则会将**数量**更改为100。
1. 查看预订的总成本。

    ![premium-ssd-reserved-selecting-sku-total-purchase .png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

购买保留后，它会自动应用到任何与保留条款相匹配的现有 Azure 磁盘存储资源。 如果尚未创建任何 Azure 磁盘存储资源，则每次创建与预订条款相匹配的资源时都将应用保留。 在这两种情况下，预订期限会在成功购买后立即开始。

## <a name="exchange-or-refund-a-reservation"></a>交换或退款预订

对于某些限制，可以交换或退款预订。

若要交换或退款预订，请导航到 Azure 门户中的预订详细信息。 选择 " **Exchange" 或 "退款**"，然后按照说明提交支持请求。 处理请求后，Microsoft 会向你发送一封电子邮件，以确认请求已完成。

有关 Azure 预订策略的详细信息，请参阅[Azure 预订的自助服务交换和退款](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>交换预订

交换预订后，您可以基于未使用的预订部分接收按比例退款。 然后，可以将该退款应用于新 Azure 磁盘预留的购买价格。
更换次数没有限制。 此外，更换也不会收取任何服务费。 您购买的新预订的值必须等于或大于原始预订的按比例信用额度。 只能为另一 Azure 磁盘保留交换 Azure 磁盘保留，而不能对任何其他 Azure 服务的保留进行交换。

### <a name="refund-a-reservation"></a>退款预订

你可以随时取消 Azure 磁盘保留。 如果取消，将根据预订的剩余期限（减去12% 的提前终止费）收到按比例退款。 每年的最大退款为 $50000。
取消预留会立即终止预订，并向 Microsoft 返回剩余月份。 剩余的按比例余额减去费用，将退还给你的原始购买形式。

## <a name="expiration-of-a-reservation"></a>保留的过期时间

保留到期时，在该保留期内使用的任何 Azure 磁盘容量按现用现付费率进行计费。 预留容量不会自动续订。
在保留期到期后的30天内，你将收到电子邮件通知，该通知将在到期日期之后再次出现。 若要继续利用预留提供的成本节约，请在过期日期之后续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预订？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何将预订折扣应用于 Azure 磁盘存储](../articles/cost-management-billing/reservations/understand-disk-reservations.md)