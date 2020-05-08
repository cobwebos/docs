---
title: 利用保留容量优化 Blob 存储的成本-Azure 存储
description: 了解如何购买 Azure 存储预留容量，以节省块 blob 和 Azure Data Lake Storage Gen2 资源的成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4ba54736abe335b53a3b8c6733063efcb34ea29a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856822"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>借助预留容量优化 Blob 存储的成本

你可以通过 Azure 存储预留容量节省 blob 数据的存储成本。 当你在一年或三年内提交到保留时，Azure 存储空间保留容量提供针对块 blob 和标准存储帐户中的 Azure Data Lake Storage Gen2 数据的容量折扣。 预订为保留期提供固定量的存储容量。

Azure 存储预留容量可显著降低块 blob 和 Azure Data Lake Storage Gen2 数据的容量成本。 节省的成本取决于你的预订持续时间、你选择保留的总容量以及你为存储帐户选择的 "访问层" 和 "冗余" 类型。 预留容量提供计费折扣，不会影响 Azure 存储资源的状态。

有关 Azure 存储预留定价的信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="reservation-terms-for-azure-storage"></a>Azure 存储的保留条款

以下部分介绍 Azure 存储预留条款。

### <a name="reservation-capacity"></a>保留容量

你可以购买 Azure 存储预留容量，单位为 100 TB，每月 1 PB，一年或三年。

### <a name="reservation-scope"></a>保留范围

Azure 存储预留容量适用于单个订阅或多个订阅（共享范围）。 仅将预订折扣应用于单个订阅，才将预订折扣应用于所选订阅。 当限定为多个订阅时，预订折扣在客户的计费上下文中跨这些订阅共享。

当你购买 Azure 存储预留容量时，你可以将保留用于块 blob 和 Azure Data Lake Storage Gen2 数据。 预订适用于你在购买范围内的使用情况，并且不能限制为订阅中的特定存储帐户、容器或对象。 不能跨多个订阅拆分保留。

Azure 存储预留只包含订阅或共享资源组中存储的数据量。 保留中不包含早期删除、操作、带宽和数据传输费用。 购买预订后，与预订属性匹配的容量费用将按折扣费率收费，而不是按即用即付费率收费。 有关 Azure 预留的详细信息，请参阅[什么是 Azure 保留？](/azure/billing/billing-save-compute-costs-reservations)。

### <a name="supported-account-types-tiers-and-redundancy-options"></a>支持的帐户类型、层和冗余选项

Azure 存储预留容量适用于标准存储帐户中的资源，包括常规用途 v2 （GPv2）和 Blob 存储帐户。

所有访问层（"热"、"冷" 和 "存档"）都支持预留。 有关访问层的详细信息，请参阅[Azure Blob 存储： "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md)。

保留支持所有类型的冗余。 有关冗余选项的详细信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md)。

> [!NOTE]
> Azure 存储预留容量不适用于高级存储帐户、常规用途 v1 （GPv1）存储帐户、Azure Data Lake Storage Gen1、页 blob、Azure 队列存储、Azure 表存储或 Azure 文件。  

### <a name="security-requirements-for-purchase"></a>购买安全要求

购买保留容量：

- 对于至少一个企业或个人订阅，你必须以即用即付费率作为**所有者**角色。
- 对于企业订阅，必须在 EA 门户中启用 "**添加保留实例**"。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
- 对于云解决方案提供商（CSP）程序，只有管理代理或销售代理可以购买 Azure Blob 存储预留容量。

## <a name="determine-required-capacity-before-purchase"></a>在购买前确定所需容量

购买 Azure 存储预留时，必须为保留选择 "区域"、"访问层" 和 "冗余" 选项。 预订仅对存储在该区域中的数据、访问层和冗余级别有效。 例如，假设你为使用区域冗余存储（ZRS）的 "美国西部" 的数据购买了预订。 对于美国东部数据、存档层中的数据或异地冗余存储（GRS）中的数据，不能使用相同的保留项。 不过，您可以根据自己的需求购买其他预订。  

保留现在适用于 100 TB 或 1 PB 块，具有较高的折扣。 当你在 Azure 门户中购买保留时，Microsoft 可能会根据你之前的使用情况提供建议，以帮助确定你应购买的预订。

## <a name="purchase-azure-storage-reserved-capacity"></a>购买 Azure 存储预留容量

可以通过[Azure 门户](https://portal.azure.com)购买 Azure 存储预留容量。 通过提前付款或按月付款的方式为预留付款。 若要详细了解每月支付的费用，请参阅[提前或按月支付购买 Azure 预订](/azure/billing/billing-monthly-payments-reservations)。

若要了解如何识别适用于你的方案的预订条款，请参阅[了解 Azure 存储预留容量折扣](../../cost-management-billing/reservations/understand-storage-charges.md)。

请按照以下步骤购买预留容量：

1. 导航到 Azure 门户中的 "[购买预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand)" 窗格。  
1. 选择 " **Azure Blob 存储**" 以购买新的保留。  
1. 填写必填字段，如下表所示：

    ![显示如何购买保留容量的屏幕截图](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |字段  |说明  |
   |---------|---------|
   |**Scope**   |  指示可以使用与预订关联的计费权益的订阅数。 它还控制将预留应用于特定订阅的方式。 <br/><br/> 如果选择 "**共享**"，则预订折扣将应用到计费上下文中任何订阅的 Azure 存储容量。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围包含由帐户管理员创建的即用即付费率的所有订阅。  <br/><br/>  如果选择 "**单个订阅**"，则预订折扣将应用到所选订阅中的 Azure 存储容量。 <br/><br/> 如果选择 "**单个资源组**"，则预订折扣将应用到所选订阅中的 Azure 存储容量和该订阅内的所选资源组。 <br/><br/> 你可以在购买预订后更改保留范围。  |
   |**订阅**  | 用于支付 Azure 存储预留的订阅。 所选订阅的付款方式用于对成本进行收费。 订阅必须是以下类型之一： <br/><br/>  企业协议（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p）：对于企业订阅，将从注册的货币承诺余额中扣除费用，或将其收费为超额。 <br/><br/> 使用即用即付费率的单个订阅（产品/服务编号： BC-OP-NT-AZR-Ms-azr-0003p 或 BC-OP-NT-AZR-0023P）：对于使用即用即付费率的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。    |
   | **区域** | 预订有效的区域。 |
   | **访问层** | 其中的预订生效的访问层。 选项包括 "*热*"、"*冷*" 或 "*存档*"。 有关访问层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md)。 |
   | **冗余** | 预订的冗余选项。 选项包括*LRS*、 *ZRS*、 *GRS*、 *GZRS*、 *ra-GRS*和*ra-GZRS*。 有关冗余选项的详细信息，请参阅[Azure 存储冗余](../common/storage-redundancy.md)。 |
   | **计费频率** | 指示帐户按预订计费的频率。 选项包括 "*每月*" 或 "*提前*"。 |
   | **大小** | 预订有效的区域。 |
   |**术语**  | 一年或三年。   |

1. 选择预订参数后，Azure 门户会显示成本。 该门户还显示 "即用即付" 计费的折扣百分比。

1. 在 "**购买预订**" 窗格中，查看预订的总成本。 还可以提供预订的名称。

    ![显示如何购买预订的屏幕截图](media/storage-blob-reserved-capacity/purchase-reservations.png)

购买保留后，它将自动应用于任何现有的 Azure 存储块 blob 或与保留条款匹配的 Azure Data Lake Storage Gen2 资源。 如果尚未创建任何 Azure 存储资源，则每次创建与预订条款相匹配的资源时，将应用保留。 在这两种情况下，预订期限会在成功购买后立即开始。

## <a name="exchange-or-refund-a-reservation"></a>交换或退款预订

可以交换或退款保留，但有一些限制。 以下部分介绍了这些限制。

若要交换或退款预订，请导航到 Azure 门户中的预订详细信息。 选择 " **Exchange** " 或 "**退款**"，然后按照说明提交支持请求。 处理请求后，Microsoft 会向你发送一封电子邮件，以确认请求已完成。

有关 Azure 预订策略的详细信息，请参阅[Azure 预订的自助服务交换和退款](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

### <a name="exchange-a-reservation"></a>交换预订

交换预订后，您可以基于未使用的预订部分接收按比例退款。 然后，可以将该退款应用于新的 Azure 存储预留的购买价格。

可以进行的交换数量没有限制。 此外，与 exchange 无关。 您购买的新预订的值必须等于或大于原始预订的按比例信用额度。 只能为另一 Azure 存储预留交换 Azure 存储预留，而不能对任何其他 Azure 服务的保留进行交换。

### <a name="refund-a-reservation"></a>退款预订

你可以随时取消 Azure 存储预留。 当你取消时，你将根据预订的剩余期限（减去12% 的提前终止费）收到按比例退款。 每年的最大退款为 $50000。

取消预留会立即终止预订，并向 Microsoft 返回剩余月份。 剩余的按比例余额减去费用，将退还给你的原始购买形式。

## <a name="expiration-of-a-reservation"></a>保留的过期时间

保留期到期后，在该保留期内使用的任何 Azure 存储容量将按即用即付费率进行计费。 预留容量不会自动续订。

在保留期到期后的30天内，你将收到电子邮件通知，该通知将在到期日期之后再次出现。 若要继续利用预留提供的成本节约，请在过期日期之后续订。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 预留项？](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [了解如何将预留折扣应用于 Azure 存储](../../cost-management-billing/reservations/understand-storage-charges.md)
