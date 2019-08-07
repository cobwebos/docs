---
title: Azure 预订的自助服务交换和退款
description: 了解如何交换或退款 Azure 预留。
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 5e38684500520d4565835456b94200aea399c938
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814111"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure 预订的自助服务交换和退款

Azure 预订提供了灵活性, 有助于满足不断发展的需求。 可以用一个预留来交换同一类型的另一个预留。 如果不再需要某个预留，也可以请求预留退款，每年最多 50,000 美元。

美国政府企业协议客户不能使用自助交换和取消功能。 支持其他美国政府订阅类型, 包括即用即付和 CSP。

您必须拥有保留订单的所有者访问权限, 才能交换或退款现有预订。

## <a name="exchange-an-existing-reserved-instance"></a>交换现有的预订实例

可以在[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中通过三个快速步骤来交换预订。

1. 选择要退款的预订, 并单击 " **Exchange**"。  
    ![显示要返回的保留的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 选择要购买的 VM 产品并键入数量。 请确保新的采购总额大于返回总计。 在[购买之前确定正确的大小](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    ![显示要使用 exchange 购买的 VM 产品的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 查看并完成该事务。  
    ![示例图像显示与 exchange 一起购买的 VM 产品, 完成返回](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

若要退款预订, 请参阅**预订详细信息**, 然后单击 "**退款**"。

## <a name="how-transactions-are-processed"></a>处理事务的方式

首先, Microsoft 取消了现有预订, 并为该预订退款了估价。 如果有 exchange, 则会处理新的购买。 Microsoft 根据你的帐户类型和付款方式, 使用以下方法之一处理退款:

### <a name="enterprise-agreement-customers"></a>企业协议客户

如果原始购买使用一次, 则将金钱承诺用于交换和退款。 自原始购买以来的任何超额发票都将重新打开并 rerated, 以确保使用货币承诺。 如果使用预订的货币承诺期限不再处于活动状态, 则会将信用额度添加到当前的企业协议货币承诺条款。

如果原始购买内容为超额购买, Microsoft 将发出信用凭单。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>即用即付发票付款和 CSP 计划

原始预订采购发票会被取消, 然后将为退款创建新发票。 对于交换, 新发票显示退款和新购买。 根据购买情况调整退款金额。 如果你只是退款了某个预订, 则按比例调整金额将保留在 Microsoft, 并将其调整为将来的预订购买。

### <a name="pay-as-you-go-credit-card-customers"></a>即用即付信用卡客户

将取消原始发票, 并创建新发票。 钱退还给用于原始购买的信用卡。 如果更改了卡, 请[联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="cancel-exchange-and-refund-policies"></a>取消、交换和退款策略

Azure 具有以下用于取消、交换和退款的策略。

**取消策略**

- 如果取消预留, 可能会提前终止 12%。
- 你为取消而收到的退款是余额提前的余额减去 12% 的提前终止费。 若要取消, 请跳到 Azure 门户中的预订, 然后选择 "**退款**"。

**Exchange 策略**

- 可以返回多个现有保留来购买同一类型的新预订。 不能为另一种类型交换保留。 例如, 你不能返回 VM 预留来购买 SQL 保留。
- 只有预订所有者才能处理交换。 [了解如何添加或更改可以管理预订的用户](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 交换将作为退款和重新购买进行处理–为取消和新购买创建不同的事务。 对于您所进行的预留, 按比例预留量将会返还。 你需要为新的购买付费。 按比例预留量是返回的预订的每日按比例剩余值。
- 即使用于购买预订的企业协议已过期, 并且已续订为新协议, 你也可以交换或退款预订。
- 你可以使用 exchange 更改任何预订属性, 如大小、区域、数量和条款。
- 新的采购总额应等于或大于返回的金额。
- 从 exchange 开始, 作为 exchange 的一部分购买的新预订具有新的条款。
- 交换没有惩罚或年度限制。

**退款策略**
- 在12个月的滚动窗口中, 总退款金额不能超过 $50000 美元。
- 根据购买价格的最低价格或预订的当前价格计算退款。
- 只有预订所有者才能处理退款。 [了解如何添加或更改可以管理预订的用户](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 尽管惩罚目前并未收费, 但 Microsoft 仍保留对任何退货收取 12% 损失的权利。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>适用于高级存储的 Exchange 非高级存储

你可以将为不支持高级存储的 VM 大小购买的预订交换为相应的 VM 大小。 例如, _f1s-f16s_的_F1_ 。 若要进行交换, 请参阅预订详细信息, 然后单击 " **exchange**"。 Exchange 不会重置保留实例的条款, 也不会创建新的事务。

## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

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
