---
title: 利用 Azure VMware 解决方案预订实例节省成本
description: 了解如何购买 Azure VMware 解决方案的预订实例。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: a477b1730d70ec729a2be333b545b6faeb009998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492377"
---
# <a name="save-costs-with-azure-vmware-solution"></a>利用 Azure VMware 解决方案节省成本

当你提交到 Azure VMware 解决方案的保留实例时，你可以节省资金。 预订折扣将自动应用于与预订范围和属性匹配的运行中的 Azure VMware 解决方案主机数。 无需将预订分配给专用主机即可获取折扣。 购买的预订实例仅涵盖使用情况的计算部分，并包括软件许可成本。 请参阅 [Azure VMware 解决方案的概述](introduction.md)。

## <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留实例可用，但有一些例外情况。

-   **云**  -预订仅在 "[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware)" 页上列出的区域中可用。

-   **配额不足**  -作用域为单个/共享订阅的保留必须在订阅中为新的保留实例提供主机配额。 你可以 [创建配额增加请求](enable-azure-vmware-solution.md) 以解决此问题。

-   **提供资格**-你将需要 [AZURE 企业协议 (EA) ](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements)   与 Microsoft 配合使用。

-   **容量限制**  -在极少数情况下，Azure 会限制购买 Azure VMware 解决方案主机 Sku 的新保留，因为区域中的容量不足。

## <a name="buy-a-reservation"></a>购买预留项

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买 Azure VMware 解决方案主机实例的保留实例。

提前支付预订费用， [按月](../cost-management-billing/reservations/prepare-buy-reservation.md)支付。

这些要求适用于购买保留的专用主机实例：

-   对于至少一个 EA 订阅或具有即用即付费率的订阅，必须是所有者角色。

-   对于 EA 订阅， **Add Reserved Instances**   必须在 [ea 门户](https://ea.azure.com/)中启用 "添加保留实例" 选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。

若要购买实例，请执行以下操作：

1. 登录  [Azure 门户](https://portal.azure.com/)。

2. 选择 " **所有服务**   >  **保留**"。

3. 选择 " **添加**"   购买新的预订，然后选择 " **Azure VMware 解决方案**"。

4. 输入必填字段。 运行与选择的属性相匹配的 Azure VMware 解决方案主机以获取预订折扣。 获取折扣的 Azure VMware 解决方案主机的实际数目取决于所选的范围和数量。

   如果你有 EA 协议，则可以使用 " **添加更多" 选项**   快速添加其他实例。 选项不适用于其他订阅类型。

   | 字段        |  说明 |
   | ------------ | ------------ |
   | 订阅 | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须是企业协议 (产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) 或 Microsoft 客户协议，或者使用即用即付费率 (产品/服务，产品/服务，) 。 从货币承诺余额中扣除费用（如果可用）或作为超额收取费用。 对于使用即用即付费率的订阅，将对订阅上的信用卡或发票付款方式收取费用。 |
   | 范围        | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：<br><ul><li><b>单个资源组范围-仅将预订折扣应用于所选资源组中的匹配资源。</li><li><b>单个订阅范围-将预订折扣应用于所选订阅中的匹配资源。</li><li><b>共享作用域-将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文为 "注册"。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。</li></ul>       |
   | 区域       | 预订涵盖的 Azure 区域。   |
   | 主机大小    | AV36    |
   | 术语         | 一年或三年。  |
   | 数量     | 预订中购买的实例数。 数量是可以获得计费折扣的正在运行的 Azure VMware 解决方案主机数。    |

## <a name="usage-data-and-reservation-utilization"></a>使用情况数据和预订利用率

对于获得预留折扣的使用量，使用数据的有效价格为零。 你可以看到哪个 Azure VMware 解决方案实例收到每个预订的预订折扣。

有关如何在使用情况数据中显示预订折扣的详细信息，以及你是 EA 客户的详细信息，请参阅 [了解适用于企业注册的 Azure 保留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)。 如果有单独的订阅，请参阅 [了解即用即付订阅的 Azure 保留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>在购买后更改保留

可以在购买后对预留进行以下类型的更改：

-   更新预留范围

-   实例大小灵活性 (（如果适用）) 

-   所有权

你还可以将预订拆分为较小的区块或合并保留。 任何更改都不会导致新的商业交易，也不会更改预订的结束日期。

>[!NOTE]
>购买预订后，你将无法直接进行以下类型的更改：
>
> - 现有预订的区域
> - SKU
> - 数量
> - Duration
>
>不过，如果想要进行更改，则可以 *交换*   预订。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预订的自助服务交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。
