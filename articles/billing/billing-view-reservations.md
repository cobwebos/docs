---
title: 查看 Azure 资源的预留 | Microsoft Docs
description: 了解如何在 Azure 门户中查看 Azure 预留。
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369118"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>在 Azure 门户中查看 Azure 保留项

根据你的订阅类型和权限，有两种方法查看 Azure 的保留项。

## <a name="view-purchased-reservations"></a>查看已购买的预订

默认情况下，在购买预留时，你和帐户管理员可以查看预留。 你和帐户管理员会自动获取预订订单和保留上的所有者角色。 若要允许其他人查看预订，必须将它们作为添加**所有者**或**读取器**预订的预订订单上。

有关详细信息，请参阅[添加或更改可以管理预留的用户](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

若要以所有者或读者身份查看预留，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“预留”中进行搜索。
    ![显示 Azure 门户的搜索的屏幕截图](./media/billing-view-reservation/portal-reservation-search.png)  
3. 此列表显示所有保留了所有者或读取器角色。 每个保留项显示的最后一个已知的使用率百分比。  
    ![显示的预订列表示例](./media/billing-view-reservation/view-reservations.png)
4. 选择一个保留项和最近五天内，请参阅利用率趋势。  
    ![示例中显示保留利用率趋势](./media/billing-view-reservation/reservation-utilization.png)
5. 此外可以获取[预订利用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)使用保留实例使用情况 API 和与[Microsoft Azure 使用情况见解 Power BI 内容包](/power-bi/service-connect-to-azure-consumption-insights)。

如果需要更改某个预留的范围，拆分某个预留，或者更改谁可以管理预留，请参阅[管理 Azure 预留](billing-manage-reserved-vm-instance.md)。

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>查看企业合约的预留交易

 如果你有合作伙伴主导的企业注册，请访问 EA 门户中的“报表”来查看预留。 对于其他企业合约，可以在 EA 门户和 Azure 门户中查看预留。 只有 EA 管理员可以查看预留交易。

若要在 Azure 门户中查看预留交易，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”中进行搜索。

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-view-reservation/portal-cm-billing-search.png)

1. 选择“预留交易”。
1. 若要筛选结果，请选择“时间范围”、“类型”或“说明”。
1. 选择“应用”。

    ![显示预留交易结果的屏幕截图](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

若要使用 API 获取数据，请参阅[获取企业客户的预留实例交易费用](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

- [什么是 Azure 的保留项？](billing-save-compute-costs-reservations.md)
- [管理 Azure 的保留项](billing-manage-reserved-vm-instance.md)

购买服务计划：

- [为 Cosmos DB 预留容量预付费](../cosmos-db/cosmos-db-reserved-capacity.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)

购买的软件计划：

- [预付款购买的 Red Hat 软件计划从 Azure 保留项](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [通过 Azure 预留为 SUSE 软件计划预付费](../virtual-machines/linux/prepay-suse-software-charges.md)

了解使用情况：

- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的预留使用情况](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
