---
title: 撤回 Azure 预留成本
description: 了解如何查看 Azure 预留费用以进行退款。
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: banders
ms.openlocfilehash: 22e2ee897236a2e079f0ce6e230502d5242a02e8
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714334"
---
# <a name="charge-back-azure-reservation-costs"></a>撤回 Azure 预留成本

企业协议和 Microsoft 客户协议计费用户可以查看预留的摊销成本数据。 他们可以使用成本数据向其合作伙伴收回订阅、资源组、资源或标记的费用。 在摊销数据中，有效价格为按比例计算的每小时预留成本。 成本是指资源在当天使用的预留总费用。

具有单独订阅的用户可以从其使用情况文件获取摊销成本数据。 当资源获取预留折扣时，使用情况文件中的 AdditionalInfo 部分会包含预留详细信息。 有关详细信息，请参阅[从 Azure 门户下载使用情况](https://docs.microsoft.com/azure/cost-management-billing/understand/download-azure-daily-usage#download-usage-from-the-azure-portal-csv)。

## <a name="get-reservation-charge-back-data-for-chargeback"></a>获取预留退款数据以进行退款

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到“成本管理 + 计费”。
1. 在“实际成本”下，选择“摊销成本”指标 。
1. 若要查看预留使用的资源，请将筛选器应用于“预留”，然后选择“预留”。
1. 将“粒度”设置为“每月”或“每日”。  
1. 将图表类型设置为“表”。
1. 将“分组依据”选项设置为“资源” 。

[![显示可用于退款的预留资源成本的示例](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

以下视频显示了如何在 Azure 门户中查看预留利用成本。

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](manage-reserved-vm-instance.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](manage-reserved-vm-instance.md)
