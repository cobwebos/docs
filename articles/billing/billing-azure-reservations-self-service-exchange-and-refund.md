---
title: Azure 预留的自助交换和退款
description: 了解如何进行 Azure 预留的交换或退款。
author: yashesvi
manager: yashesvi
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 87f8a90b2213c5e78b456477e8be61d2f013fd71
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224116"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure 预留的自助交换和退款

Azure 预留可以灵活地满足你不断变化的需求。 可以用一个预留来交换同一类型的另一个预留。 如果不再需要某个预留，也可以请求预留退款，每年最多 50,000 美元。

美国政府企业协议客户不能使用自助交换和取消功能。 可以使用其他的美国政府订阅类型，包括即用即付和 CSP。

若要请求对现有的预留进行交换或退款，你必须具有预留订单的所有者访问权限。

## <a name="exchange-an-existing-reserved-instance"></a>交换现有的预留实例

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中通过三个快速步骤来交换预留。

1. 选择要退款的预留，然后单击“交换”。   
    ![显示要返回的预留的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 选择要购买的 VM 产品并键入数量。 确保新的采购总计超出返回总计。 [购买之前确定正确的大小](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    ![显示要通过交换进行购买的 VM 产品的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 复查并完成交易。  
    ![显示要通过交换进行购买的 VM 产品的示例图像，完成返回](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

若要请求预留退款，请转到“预留详细信息”，单击“退款”。  

## <a name="how-transactions-are-processed"></a>处理交易的方式

首先，Microsoft 取消现有的预留，然后退回针对该预留进行按比例计价的款项。 如果存在交换，则处理新的购买。 Microsoft 使用以下方法之一处理退款，具体取决于你的帐户类型和付款方式：

### <a name="enterprise-agreement-customers"></a>企业协议客户

款项将添加到进行交换和退款的货币承诺，前提是原始购买是使用其中一个进行的。 任何自原始购买以来生成的超额发票会重新打开并重新计费，确保使用货币承诺。 如果通过预留购买的货币承诺期限不再有效，则会将额度添加到当前的企业协议货币承诺期限。 此额度自退款之日起 90 天内有效。 未使用的额度在 90 天结束之时过期。

如果原始购买是以超额形式完成的，则 Microsoft 会发出信用票据。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>即用即付发票款项和 CSP 计划

先取消原始预留购买发票，然后针对退款创建新发票。 对于交换，新发票会显示退款和新的购买。 退款金额根据购买进行调整。 如果你只要求预留退款，则按比例调整后的金额将留给 Microsoft，针对将来的预留购买再进行调整。

### <a name="pay-as-you-go-credit-card-customers"></a>即用即付信用卡客户

先取消原始发票，然后创建新发票。 款项退给用于原始购买的信用卡。 如果你更改了信用卡，请[联系支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="cancel-exchange-and-refund-policies"></a>取消、交换和退款政策

对于取消、交换和退款，Azure 提供以下策略。

**交换策略**

- 可以返回多个现有的预留，以便购买同一类型的新预留。 不能使用一种类型的预留来交换另一种类型的预留。 例如，不能通过退回 VM 预留的方式来购买 SQL 预留。
- 只有预留所有者可以处理交换。 [了解如何添加或更改可以管理预留的用户](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 交换以退款和重新购买的形式处理 – 将为取消和新的购买创建不同的交易。 将会针对折价的预留按比例退回预留金额。 新的购买按全价收费。 按比例计价的预留金额是要返回的预留的每日按比例计价的剩余价值。
- 即使用于购买预留的企业协议过期且已作为新协议续订，你也可以请求进行预留交换或退款。
- 可以通过交换更改任何预留属性，例如大小、区域、数量以及期限。
- 新购买的金额总计应等于或大于返回的金额。
- 在交换过程中购买的新预留有一个新的期限，其开始时间为进行交换的时间。
- 进行交换时，没有罚金，也没有年度限制。

**退款策略**
- 如果取消预留，可能会产生 12% 的提前终止费。
- 进行取消时收到的退款为按比例计算出的余额减去 12% 的提前终止费。 若要取消，请在 Azure 门户中访问该预留，并选择“退款”  。
- 在为期 12 个月的滚动时限中，总退款金额不能超过 50,000 美元。
- 退款根据购买价格和当前预留价格中的最低者计算。
- 只有预留所有者可以处理退款。 [了解如何添加或更改可以管理预留的用户](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 对于任何退货，Microsoft 保留收取 12% 罚金的权利。 罚金目前不会收取，但会在以后收取。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>以非高级存储交换高级存储

可以将购买时其 VM 大小不支持高级存储的预留交换成其 VM 大小支持高级存储的预留。 例如，以 _F1_ 交换 _F1s_。 若要进行交换，请转到“预留详细信息”，单击“交换”。  交换不会重置预留实例的期限，也不创建新的交易。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](billing-manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](billing-manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](billing-understand-vm-reservation-charges.md)
    - [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
    - [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)
