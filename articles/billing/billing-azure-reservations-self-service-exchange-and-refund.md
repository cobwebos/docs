---
title: 自助服务交换和 Azure 保留项的退款 |Microsoft Docs
description: 了解如何可以交换或退款 Azure 保留项。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/5/2019
ms.author: banders
ms.openlocfilehash: aa1a218fbf0bc7eacac65b50e4ee1f86791e2b3b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281975"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>自助服务交换和退款 Azure 保留项

Azure 保留提供弹性，以帮助满足不断发展的需要。 你可以在交换为相同类型的另一个预订的保留。 如果不再需要还可以退款保留项，最多 50,000 美元每年。

自助服务交换和取消功能不可用于美国政府企业协议客户。 支持其他美国政府版订阅类型，包括即用即付和 CSP。

## <a name="exchange-an-existing-reserved-instance"></a>交换现有的保留的实例

你可以在交换中的三个快速步骤与预订[Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。

1. 选择你想要退款和单击的预订**Exchange**。  
    ![显示要返回的预订的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 选择你想要购买并键入数量的 VM 产品。 请确保新的购买总不只是返回总计。 [确定适当的大小，在购买之前](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    ![显示 VM 产品购买进行交换的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 查看并完成该事务。  
    ![显示 VM 产品购买进行交换，完成返回的示例图像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

若要退款预订，请转到**预订详细信息**然后单击**退款**。

## <a name="how-return-and-exchange-transactions-are-processed"></a>如何返回和 exchange 事务进行处理

首先，Microsoft 将取消现有的保留和退款该预订的按比例金额。 如果交换，处理新购买的产品。 Microsoft 处理退款使用以下方法，具体取决于你的帐户类型和支付方法之一：

### <a name="enterprise-agreement-customers"></a>企业协议的客户

Money 添加到交换的货币承诺和退款如果原始购买了使用其中一个。 使用任何超额费用发票，因为原始购买都重新打开，rerated 以确保资金承诺。 如果使用购买预订的货币承诺术语不再处于活动状态，然后信用额度添加到你当前的企业协议货币承诺条款。

如果原始购买了作为超额，Microsoft 会发出贷项通知单。

### <a name="pay-as-you-go-invoice-payment-customers-and-cloud-solution-provider-program"></a>即用即付发票付款客户和云解决方案提供商计划

原始的预订采购发票将取消，则会针对退款创建新的发票。 对于交换，新发票显示退款和新的购买。 针对购买，退款金额会进行调整。 如果仅退还一个保留项，然后按比例分配的量保持不变，Microsoft，并调整对未来的预订购买。

### <a name="pay-as-you-go-credit-card-customers"></a>即用即付信用卡客户

取消将原始发票，并创建新的发票。 资金退还给用于原始购买的信用卡。 如果你已更改您的卡[请联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="exchange-policies"></a>Exchange 策略

- 您可以返回多个现有预订购买相同类型的新的保留。 您不能交换另一种类型的保留的项。 例如，不能返回 VM 保留项，以购买 SQL 保留。
- 仅保留项所有者可以处理交换。 [了解如何添加或更改的用户可以管理预订](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)。
- 作为退款并重新处理交换 – 用于取消和新采购创建不同的事务。 每天按比例分摊的保留金额退款的预留您折旧。 我们的新购买完全收费。 每天按比例分摊的预留量是保留的返回的每日按比例分配剩余值。
- 你可以在交换或退款保留即使企业协议用于购买预订已过期，已作为新的协议续订。
- 可以更改任何保留属性，如大小、 区域、 数量和术语进行交换。
- 新的购买总应等于或大于返回量。
- 作为交换的一部分购买新的保留已从 exchange 时开始一个新术语。
- 没有损失或每年限制进行交换。

## <a name="refund-policies"></a>退款政策

- 退款金额不能超过 12 个月滚动窗口中 50,000 美元。
- 仅保留项所有者可以处理退款。 [了解如何添加或更改的用户可以管理预订](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 尽管不当前计费损失，Microsoft 保留权收取的任何返回时，对 12%产生负面影响。

## <a name="exchange-a-non-premium-storage-vm-reservation-for-a-premium-storage-reservation"></a>交换非高级存储 VM 的高级存储保留的保留空间

你可以在不支持为相应的 VM 大小的高级存储的 VM 大小为购买的保留交换。 例如， _F1_有关_F1s_。 若要进行交换，请转到预订详细信息，然后单击**Exchange**。 Exchange 不会重置保留实例的术语或创建新的事务。

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
