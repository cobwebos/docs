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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590708"
---
使用预留容量保存 Azure 磁盘存储使用情况。 Azure 磁盘存储预留与 Azure 预留虚拟机实例相结合，可降低虚拟机 （VM） 总成本。 预留折扣将自动应用于所选预留范围内的匹配磁盘。 由于此自动应用程序，您无需将预留分配给托管磁盘以获得折扣。

折扣每小时应用一次，具体取决于磁盘使用情况。 未使用的预留容量不会保留。 Azure 磁盘存储预留折扣不适用于非托管磁盘、超级磁盘或页面 Blob 消耗。

## <a name="determine-your-storage-needs"></a>确定您的存储需求

在购买预订之前，请确定您的存储需求。 目前，Azure 磁盘存储预留仅适用于选定的 Azure 高级 SSD SSD SKU。 高级 SSD 的 SKU 确定磁盘的大小和性能。

在确定存储需求时，不要仅仅考虑基于容量的磁盘。 例如，不能预订 P40 磁盘，而无法使用该预留来为两个较小的 P30 磁盘付费。 购买预订时，您只为每个 SKU 购买磁盘总数的预留。

每个磁盘 SKU 进行磁盘预留。 因此，预留消耗量基于磁盘 SKU 的单位，而不是提供的大小。

例如，假设您保留一个 P40 磁盘，该磁盘具有 2 TiB 的预配存储容量。 还假定您只分配两个 P30 磁盘。 在这种情况下，P40 预留不包括 P30 消耗量，您在 P30 磁盘上支付即用即付费率。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>购买注意事项

在考虑购买磁盘时，我们建议采用以下做法：

- 分析使用情况信息，以帮助确定应购买哪些预订。 请确保跟踪磁盘 SKU 中的使用情况，而不是预配或使用的磁盘容量。
- 检查磁盘保留以及 VM 预留。 我们强烈建议为 VM 使用和磁盘使用情况进行预留，以最大限度地节省成本。 您可以先确定正确的 VM 预留，然后评估磁盘预留。 通常，您将为每个工作负载提供标准配置。 例如，SQL Server 服务器可能具有两个 P40 数据磁盘和一个 P30 操作系统磁盘。
  
  这种模式可以帮助您确定可能购买的预留金额。 此方法可以简化评估过程，并确保 VM 和磁盘都有一致的计划。 该计划包含订阅或区域等注意事项。

## <a name="purchase-restrictions"></a>购买限制

预订折扣当前不适用于以下事项：

- 非托管磁盘或页面 blob。
- 标准 SSD 或标准硬盘 （HDD）。
- 小于 P30 的高级 SSD SKU：P1、P2、P3、P4、P6、P10、P15 和 P20 SSD SKU。
- Azure 政府、Azure 德国或 Azure 中国区域中的磁盘。

在极少数情况下，Azure 会将购买新预留限制为磁盘 SKU 的子集，因为区域中的容量较低。

## <a name="buy-a-disk-reservation"></a>购买磁盘预订

您可以通过[Azure 门户](https://portal.azure.com/)购买 Azure 磁盘存储预留。 您可以提前或按月付款支付预订费用。 有关使用每月付款进行购买的详细信息，请参阅[每月付款购买预订](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)。

按照以下步骤购买预留容量：

1. 转到 Azure 门户中的["购买预留"](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)窗格。

1. 选择**Azure 托管磁盘**以购买预留。

    ![用于购买预订的窗格](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 指定下表中描述的所需值：

   |元素  |描述  |
   |---------|---------|
   |**范围**   |  有多少订阅可以使用与预订关联的计费权益。 此值还指定如何将预留应用于特定订阅。 <br/><br/> 如果选择 **"共享**"，则预留折扣将应用于计费上下文中每个订阅中的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包括帐户管理员创建的所有个人订阅，即用即用即付费率。  <br/><br/>  如果选择 **"单一订阅**"，则预留折扣将应用于所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择 **"单个资源组**"，则预留折扣将应用于所选订阅中以及该订阅的选定资源组中的 Azure 存储容量。 <br/><br/> 您可以在购买预订后更改预订范围。  |
   |**订阅**  | 用于为 Azure 存储预留付费的订阅。 所选订阅的付款方式用于收取费用。 订阅必须是以下类型之一：<br/><ul><li> 企业协议（产品编号 MS-AZR-0017P 和 MS-AZR-0148P）。 对于企业订阅，从注册的货币承诺余额中扣除费用或作为超额收取费用。</li><br/><li>个人订阅，即用即付费率（产品编号 MS-AZR-0003P 和 MS-AZR-0023P）。 对于具有即用即付费率的个人订阅，费用将计入订阅上的信用卡或发票付款方式。</li></ul>    |
   | **磁盘** | 要创建的 SKU。 |
   | **地区** | 保留生效的区域。 |
   | **计费频率** | 帐户的预订费用。 选项包括**每月**和**预付**。 |

    ![用于选择要购买的产品的窗格。](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 指定预留值后，Azure 门户将显示成本。 该门户还显示与即用即付计费的折扣百分比。 选择 **"下一步**"以继续"**购买预订"** 窗格。

1. 在 **"购买预订"** 窗格中，您可以命名预订并选择要预订的总数。 预留数映射到磁盘数。 例如，如果要保留一百个磁盘，请输入**数量**值**100**。

1. 查看预订的总成本。

    !["购买预订"窗格](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

购买预留后，它将自动应用于与预订条款匹配的任何现有磁盘存储资源。 如果您尚未创建任何磁盘存储资源，则每当创建与保留条款匹配的资源时，保留都会适用。 在这两种情况下，预订期限在成功购买后立即开始。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

您可以在某些限制范围内取消、交换或退款预订。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)。

## <a name="expiration-of-a-reservation"></a>预订到期

当预留过期时，您在该预留下使用的任何 Azure 磁盘存储容量都将以即用即付费率计费。 预留容量不会自动续订。

您将在预订到期前 30 天收到电子邮件通知，并在到期日期再次收到通知。 要继续利用预订提供的成本节约，请不迟于到期日期续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预留？](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解预留折扣如何应用于 Azure 磁盘存储](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
