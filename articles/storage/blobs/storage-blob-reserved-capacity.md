---
title: 使用预留容量优化 Blob 存储的成本 - Azure 存储
description: 了解如何购买 Azure 存储预留容量，以节省块 Blob 和 Azure 数据存储湖存储 Gen2 资源的成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351033"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>借助预留容量优化 Blob 存储的成本

您可以使用 Azure 存储预留容量为 Blob 数据节省存储成本。 当您提交一年或三年的预留时，Azure 存储预留容量可为标准存储帐户中的块 Blob 和 Azure 数据存储库存储 Gen2 数据的容量提供折扣。 预订提供预订期限的固定存储容量。

Azure 存储预留容量可以显著降低块 Blob 和 Azure 数据存储湖存储 Gen2 数据的容量成本。 实现的成本节约取决于预订的持续时间、您选择预订的总容量以及您为存储帐户选择的冗余访问层和类型。 预留容量提供计费折扣，不会影响 Azure 存储资源的状态。

有关 Azure 存储预留定价的信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="reservation-terms-for-azure-storage"></a>Azure 存储的预留条款

以下各节介绍 Azure 存储预留的术语。

### <a name="reservation-capacity"></a>预订容量

您可以购买 Azure 存储预留容量，单位为 100 TB，每月购买 1 PB，为期一年或三年。

### <a name="reservation-scope"></a>预订范围

Azure 存储预留容量可用于单个订阅或多个订阅（共享范围）。 当限定为单个订阅时，保留折扣将仅应用于选定的订阅。 当范围限定为多个订阅时，预留折扣将在客户的计费上下文中在这些订阅中共享。

购买 Azure 存储预留容量时，可以使用预留块 Blob 和 Azure 数据湖存储 Gen2 数据。 预留适用于您在购买范围内的使用，不能仅限于订阅中的特定存储帐户、容器或对象。 无法跨多个订阅拆分预留。

Azure 存储预留仅涵盖存储在订阅或共享资源组中的数据量。 保留中不包括早期删除、操作、带宽和数据传输费用。 购买预订后，将按折扣费率而不是即付即用费率收取与预订属性匹配的容量费用。 有关 Azure 预留的详细信息，请参阅[什么是 Azure 预留？](/azure/billing/billing-save-compute-costs-reservations)

### <a name="supported-account-types-tiers-and-redundancy-options"></a>受支持的帐户类型、层和冗余选项

Azure 存储预留容量可用于标准存储帐户中的资源，包括通用 v2 （GPv2） 和 Blob 存储帐户。

所有访问层（热、酷和存档）都支持预订。 有关访问层的详细信息，请参阅 Azure [Blob 存储：热、酷和存档访问层](storage-blob-storage-tiers.md)。

所有类型的冗余都支持进行预留。 有关冗余选项的详细信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md)。

> [!NOTE]
> Azure 存储预留容量不适用于高级存储帐户、通用 v1 （GPv1） 存储帐户、Azure 数据湖存储第 1 代、页面 Blob、Azure 队列存储、Azure 表存储或 Azure 文件。  

### <a name="security-requirements-for-purchase"></a>购买的安全要求

要购买预留容量：

- 您必须在至少一个企业或个人订阅中处于 **"所有者**"角色，该订阅具有即用即付费率。
- 对于企业订阅，必须在 EA 门户中启用 **"添加预留实例**"。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商 （CSP） 计划，只有管理员代理或销售代理才能购买 Azure Blob 存储预留容量。

## <a name="determine-required-capacity-before-purchase"></a>购买前确定所需容量

购买 Azure 存储预留时，必须选择预留的区域、访问层和冗余选项。 您的预订仅适用于存储在该区域、访问层和冗余级别的数据。 例如，假设您使用区域冗余存储 （ZRS） 为热层购买美国西部数据预留。 对于美国东部的数据、存档层中的数据或异地冗余存储 （GRS） 中的数据，不能使用相同的预留。 但是，您可以购买其他预订，以满足您的其他需求。  

现在，100 TB 或 1 PB 模块的预订提供，1 个 PB 模块的折扣更高。 在 Azure 门户中购买预留时，Microsoft 可能会根据以前的使用情况为您提供建议，以帮助确定应购买哪个预留。

## <a name="purchase-azure-storage-reserved-capacity"></a>购买 Azure 存储预留容量

您可以通过[Azure 门户](https://portal.azure.com)购买 Azure 存储预留容量。 通过提前付款或按月付款的方式为预留付款。 有关使用每月付款进行购买的详细信息，请参阅[使用预付或每月付款购买 Azure 预订](/azure/billing/billing-monthly-payments-reservations)。

有关确定适合方案的预留条款的帮助，请参阅[了解 Azure 存储预留容量折扣](../../cost-management-billing/reservations/understand-storage-charges.md)。

按照以下步骤购买预留容量：

1. 导航到 Azure 门户中的["购买预留"](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)窗格。  
1. 选择**Azure Blob 存储**以购买新预留。  
1. 填写必填字段，如下表所示：

    ![显示如何购买预留容量的屏幕截图](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |字段  |描述  |
   |---------|---------|
   |**范围**   |  指示有多少订阅可以使用与预订关联的计费权益。 它还控制将预留应用于特定订阅的方式。 <br/><br/> 如果选择 **"共享**"，则预留折扣将应用于计费上下文中任何订阅中的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包括帐户管理员创建的所有个人订阅，即用即用即付费率。  <br/><br/>  如果选择 **"单一订阅**"，则预留折扣将应用于所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择 **"单个资源组**"，则预留折扣将应用于所选订阅中的 Azure 存储容量以及该订阅中的选定资源组。 <br/><br/> 您可以在购买预订后更改预订范围。  |
   |**订阅**  | 用于为 Azure 存储预留付费的订阅。 所选订阅的付款方式用于收取费用。 订阅必须是以下类型之一： <br/><br/>  企业协议（优惠编号：MS-AZR-0017P 或 MS-AZR-0148P）：对于企业订阅，费用从注册的货币承诺余额中扣除，或按超额收取。 <br/><br/> 具有即用即付费率的个人订阅（优惠编号：MS-AZR-0003P 或 MS-AZR-0023P）：对于具有即用即付费率的个人订阅，费用由订阅上的信用卡或发票付款方式计费。    |
   | **地区** | 保留生效的区域。 |
   | **访问层** | 保留生效的接入层。 选项包括*热*、*酷*或*存档*。 有关访问层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。 |
   | **冗余** | 预留的冗余选项。 选项包括*LRS、ZRS、GRS*和*RA-GZRS*。 *LRS* *GRS* 有关冗余选项的详细信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md)。 |
   | **计费频率** | 指示帐户为预订计费的频率。 选项包括*每月*或*预付*。 |
   | **大小** | 保留生效的区域。 |
   |**术语**  | 一年或三年。   |

1. 选择预留的参数后，Azure 门户将显示成本。 该门户还显示与即用即付计费的折扣百分比。

1. 在 **"购买预订"** 窗格中，查看预订的总成本。 您还可以提供预订的名称。

    ![显示如何购买预订的屏幕截图](media/storage-blob-reserved-capacity/purchase-reservations.png)

购买预留后，它将自动应用于任何与预留条款匹配的现有 Azure 存储块 Blob 或 Azure 数据湖存储 Gen2 资源。 如果尚未创建任何 Azure 存储资源，则每当创建与保留条款匹配的资源时，都将应用预留。 在这两种情况下，预订期限在成功购买后立即开始。

## <a name="exchange-or-refund-a-reservation"></a>交换或退还预订

您可以兑换或退款预订，但有一定的限制。 这些限制在以下各节中介绍。

要交换或退还预留，请导航到 Azure 门户中的预留详细信息。 选择 **"兑换**"或 **"退款**"，然后按照说明提交支持请求。 处理请求后，Microsoft 将向您发送一封电子邮件以确认请求的完成。

有关 Azure 预留策略的详细信息，请参阅[Azure 预留的自助服务交换和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>交换预订

通过交换预订，您可以根据预订中未使用的部分获得按比例退款。 然后，您可以将退款应用于新的 Azure 存储预留的购买价格。

您可以进行的交流数量没有限制。 此外，不收取与交易所相关的费用。 您购买的新预订的价值必须等于或大于原始预订的按比例抵免额。 Azure 存储预留只能换用于其他 Azure 存储预留，而不能为任何其他 Azure 服务进行预留。

### <a name="refund-a-reservation"></a>退还预订

您可以随时取消 Azure 存储预留。 取消后，您将根据预订的剩余期限按比例退款，减去 12% 的提前终止费。 每年的最高退款额为 50，000 美元。

取消预订会立即终止预订，并将剩余月份返还给 Microsoft。 剩余的按比例余额（减去费用）将退还至您原始购买形式。

## <a name="expiration-of-a-reservation"></a>预订到期

当预订到期时，您根据该预留使用的任何 Azure 存储容量都将以即用即付费率计费。 预留容量不会自动续订。

您将在预订到期前 30 天收到电子邮件通知，并在到期日期再次收到通知。 要继续利用预订提供的成本节约，请不迟于到期日期续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预留？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何将预留折扣应用于 Azure 存储](../../cost-management-billing/reservations/understand-storage-charges.md)
