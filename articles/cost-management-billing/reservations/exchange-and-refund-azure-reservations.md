---
title: Azure 预留的自助交换和退款
description: 了解如何进行 Azure 预留的交换或退款。
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 3f41f26e6934e156ab38340a74e2c070b26cefb4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287652"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure 预留的自助交换和退款

Azure 预留可以灵活地满足你不断变化的需求。 可以用一个预留来交换同一类型的另一个预留。 例如，你可以通过交换虚拟机预留来购买其他任何 VM 大小或区域的另一个预留。 同样，可以交换 SQL PaaS 数据库预留以购买任何 SQL PaaS 数据库类型或区域的其他预留。 还可以对预留申请退款，但在 12 个月的滚动范围内，计费范围内（如 EA、Microsoft 客户协议和 Microsoft 合作伙伴协议）所有取消的预留承诺总额不得超过 50,000 美元。 Azure Databricks 预留容量、Azure VMware Solution by CloudSimple 预留、Azure Red Hat Open Shift 预留、Red Hat 计划和 SUSE Linux 计划不能申请退款。

美国政府企业协议客户不能使用自助交换和取消功能。 可以使用其他美国政府订阅类型，包括即用即付和云解决方案提供商 (CSP)。

> [!NOTE]
> - 若要请求对现有的预留进行交换或退款，你必须具有预留订单的所有者访问权限。 可[添加或更改可以管理预留的用户](https://docs.microsoft.com/azure/cost-management-billing/reservations/manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)。
> - Microsoft 目前不会对预留退款收取提前终止费。 但我们可能会对将来的退款收取该费用。 目前尚未确定收取此费用的日期。

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>如何对现有预留进行交换或退款

你可以从 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)交换预留。

1. 选择要退款的预留，然后选择“交换”。  
    [![显示要返回的预留的示例图像](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. 选择要购买的 VM 产品并键入数量。 确保新的采购总计超出返回总计。 [购买之前确定正确的大小](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    [![显示要通过交换进行购买的 VM 产品的示例图像](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. 复查并完成交易。  
    [![显示要通过交换进行购买的 VM 产品的示例图像，完成返回](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

若要为某个预留退款，请转到“预留详细信息”，然后选择“退款”。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>以非高级存储交换高级存储

可以将购买时其 VM 大小不支持高级存储的预留交换成其 VM 大小支持高级存储的预留。 例如，以 _F1_ 交换 _F1s_。 若要进行交换，请转到“预留详细信息”，然后选择“交换”。 交换不会重置预留实例的期限，也不创建新的交易。 

## <a name="how-transactions-are-processed"></a>处理交易的方式

首先，Microsoft 取消现有的预留，然后退回针对该预留进行按比例计价的款项。 如果存在交换，则处理新的购买。 Microsoft 使用以下方法之一处理退款，具体取决于你的帐户类型和付款方式：

### <a name="enterprise-agreement-customers"></a>企业协议客户

款项将添加到进行交换和退款的货币承诺，前提是原始购买是使用其中一个进行的。 如果通过预留购买的货币承诺期限不再有效，则会将额度添加到当前的企业协议货币承诺期限。 此额度自退款之日起 90 天内有效。 未使用的额度在 90 天结束之时过期。

如果原始购买是以超额形式完成的，则购买预留的原始发票和所有后续发票都会重新开具和重新调整。 Microsoft 针对退款发出贷项凭单。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>即用即付发票款项和 CSP 计划

先取消原始预留购买发票，然后针对退款创建新发票。 对于交换，新发票会显示退款和新的购买。 退款金额根据购买进行调整。 如果你只要求预留退款，则按比例调整后的金额将留给 Microsoft，针对将来的预留购买再进行调整。 如果你以即用即付费率购买了预留，随后又转到 CSP，则可以退回预留并重新购买，无需支付罚金。

### <a name="pay-as-you-go-credit-card-customers"></a>即用即付信用卡客户

先取消原始发票，然后创建新发票。 款项退给用于原始购买的信用卡。 如果你更改了信用卡，请[联系支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="cancel-exchange-and-refund-policies"></a>取消、交换和退款政策

对于取消、交换和退款，Azure 提供以下策略。

**交换策略**

- 可以退回多个现有的预留，以便购买同一类型的新预留。 不能使用一种类型的预留来交换另一种类型的预留。 例如，不能通过退回 VM 预留的方式来购买 SQL 预留。 可以通过交换更改预留属性，例如家族、系列、版本、SKU、区域、数量以及期限。
- 只有预留所有者可以处理交换。 [了解如何添加或更改可以管理预留的用户](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 交换以退款和重新购买的形式处理 - 将为取消和新的预留购买创建不同的交易。 针对折价的预留按比例退回预留金额。 新的购买按全价收费。 按比例计价的预留金额是要返回的预留的每日按比例计价的剩余价值。
- 即使用于购买预留的企业协议过期且已作为新协议续订，你也可以请求进行预留交换或退款。
- 新预留的生存期承诺应等于或大于退回的预留的剩余承诺。 示例：对于一个为期三年的预留，每月支付 100 美元，在第 18 次付款后交换，则新预留的生存期承诺应为 1,800 美元或更高（每月支付或提前支付）。
- 在交换过程中购买的新预留有一个新的期限，其开始时间为进行交换的时间。
- 进行交换时，没有罚金，也没有年度限制。

**退款策略**

- 我们目前不会收取提前终止费，但将来可能会收取因取消而产生的 12% 的提前终止费。
- 对于计费对象信息或单个注册，取消的承诺总额在 12 个月滚动范围内不得超过 50,000 美元。 例如，对于三年的预留，每月支付 100 美元并在第 18 个月退款，则取消的承诺为 1,800 美元。 退款后，新的可用退款限额为 48,200 美元。 此退款的 365 天后，48,200 美元限额将增加 1800 美元，而新的池将为 50,000 美元。 计费对象信息或 EA 注册的所有其他预留取消将消耗同一池中的额度，并且适用于同一补充逻辑。
- 对于计费对象信息或 EA 注册在 12 个月的时间段内超过 50,000 美元限额的任何退款，Azure 将不予处理。
- 退款根据购买价格和当前预留价格中的最低者计算。
- 只有预留订单所有者可以处理退款。 [了解如何添加或更改可以管理预留的用户](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
    - [什么是 Azure 预订？](save-compute-costs-reservations.md)
    - [管理 Azure 中的预留](manage-reserved-vm-instance.md)
    - [了解预留折扣的应用方式](../manage/understand-vm-reservation-charges.md)
    - [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)
    - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
    - [预留未包含的 Windows 软件成本](reserved-instance-windows-software-costs.md)
    - [CSP 计划中的 Azure 预留](/partner-center/azure-reservations)
