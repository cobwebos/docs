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
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590708"
---
使用保留容量节省 Azure 磁盘存储使用量。 与 Azure 保留虚拟机实例结合 Azure 磁盘存储保留，可降低总虚拟机（VM）的成本。 预订折扣将自动应用于所选预订范围内的匹配磁盘。 由于这种自动应用程序，你无需为托管磁盘分配预留来获取折扣。

每小时按磁盘使用情况应用折扣。 未使用的保留容量不会超过。 Azure 磁盘存储预订折扣不适用于非托管磁盘、超磁盘或页 blob 消耗。

## <a name="determine-your-storage-needs"></a>确定存储需求

在购买保留之前，请确定你的存储需求。 目前，Azure 磁盘存储预订仅适用于选择 Azure 高级 SSD Sku。 高级 SSD 的 SKU 决定磁盘的大小和性能。

确定存储需求时，请不要只考虑磁盘容量。 例如，你不能保留 P40 磁盘，并使用它来支付两个较小的 P30 磁盘。 购买预订时，只需购买每个 SKU 的磁盘总数保留。

磁盘保留按磁盘 SKU 进行。 因此，保留消耗基于磁盘 Sku 的单位，而不是所提供的大小。

例如，假设你保留一个 P40 磁盘，其中有2个 TiB 的预配存储容量。 还假设只分配两个 P30 磁盘。 在这种情况下，P40 预订不会考虑 P30 消耗，并按即用即付费率支付 P30 磁盘。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>购买注意事项

考虑进行磁盘保留购买时，建议采用以下做法：

- 分析你的使用情况信息，以帮助确定你应购买哪些预订。 请确保跟踪磁盘 Sku 中的使用情况，而不是预配或使用的磁盘容量。
- 检查磁盘保留，以及 VM 预留。 我们强烈建议为 VM 使用情况和磁盘使用情况进行预留，以实现最大的节约。 你可以从确定正确的 VM 预留开始，然后评估磁盘预留。 通常，每个工作负荷都有一个标准配置。 例如，SQL Server 服务器可能有两个 P40 数据磁盘和一个 P30 操作系统磁盘。
  
  这种模式可帮助您确定您可能购买的预留量。 此方法可以简化评估过程，并确保已为 VM 和磁盘提供协调计划。 计划包含订阅或区域等注意事项。

## <a name="purchase-restrictions"></a>购买限制

预订折扣当前不可用于以下各项：

- 非托管磁盘或页 blob。
- 标准 Ssd 或标准硬盘（Hdd）。
- 高级 SSD Sku 小于 P30： P1、P2、P3、P4、P6、P10、P15 和 P20 SSD Sku。
- Azure 政府、Azure 德国或 Azure 中国区中的磁盘。

在极少数情况下，Azure 会将新保留的购买范围限制为磁盘 Sku 的子集，因为区域中的容量不足。

## <a name="buy-a-disk-reservation"></a>购买磁盘保留

您可以通过[Azure 门户](https://portal.azure.com/)购买 Azure 磁盘存储预订。 你可以为预留提前支付或按月支付。 有关每月支付购买的详细信息，请参阅[每月支付的购买预留](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。

请按照以下步骤购买预留容量：

1. 中转到 Azure 门户中的 "[购买预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)" 窗格。

1. 选择 " **Azure 托管磁盘**" 以购买预订。

    ![采购预订的窗格](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 指定下表中所述的所需值：

   |元素  |说明  |
   |---------|---------|
   |**范围**   |  多少订阅可以使用与预订相关的计费权益。 此值还指定如何将预订应用到特定订阅。 <br/><br/> 如果选择 "**共享**"，预订折扣将应用到计费上下文中每个订阅的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包含由帐户管理员创建的即用即付费率的所有订阅。  <br/><br/>  如果选择 "**单个订阅**"，则预订折扣将应用到所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择 "**单个资源组**"，则预订折扣将应用到所选订阅和该订阅所选资源组中的 Azure 存储容量。 <br/><br/> 你可以在购买预订后更改保留范围。  |
   |**订阅**  | 用于支付 Azure 存储预留的订阅。 所选订阅的付款方式用于对成本进行收费。 订阅必须是以下类型之一：<br/><ul><li> 企业协议（BC-OP-NT-AZR-Ms-azr-0017p 和 BC-OP-NT-AZR-Ms-azr-0148p）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。</li><br/><li>按现用现付费率（产品/服务-BC-OP-NT-AZR-Ms-azr-0003p 和 BC-OP-NT-AZR-0023P）的单个订阅。 对于使用即用即付费率的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。</li></ul>    |
   | **磁盘** | 要创建的 SKU。 |
   | **区域** | 预订有效的区域。 |
   | **计费频率** | 帐户按预订计费的频率。 选项包括**每月**和**前期**。 |

    ![用于选择要购买的产品的窗格。 png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 指定预订值后，Azure 门户将显示成本。 该门户还显示 "即用即付" 计费的折扣百分比。 选择 "**下一步**" 以继续转到 "**购买预订**" 窗格。

1. 在 "**购买预订**" 窗格上，可以命名预订，并选择要进行的保留的总数量。 预订数映射到磁盘数。 例如，如果想要保留一百个磁盘，请输入**数量**值**100**。

1. 查看预订的总成本。

    !["购买预订" 窗格](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

购买预订后，它会自动应用于与预订条款匹配的任何现有磁盘存储资源。 如果尚未创建任何磁盘存储资源，则每次创建与预订条款相匹配的资源时，预订都适用。 在这两种情况下，预订期限会在成功购买后立即开始。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

您可以在某些限制条件下取消、交换或退款。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="expiration-of-a-reservation"></a>保留的过期时间

保留期到期后，在该保留期内使用的任何 Azure 磁盘存储容量将按现用现付费率进行计费。 预留容量不会自动续订。

你将在预订过期前30天收到一封电子邮件通知，并在到期日期之后收到电子邮件通知。 若要继续利用预留提供的成本节约，请在过期日期之后续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预订？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何将预订折扣应用于 Azure 磁盘存储](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
