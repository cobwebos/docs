---
title: 查看 Azure 资源的预留 | Microsoft Docs
description: 了解如何在 Azure 门户中查看 Azure 预留。 使用 API、PowerShell、CLI 和 Power BI 查看预留项和利用率。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 04c38f7445dc0405add88fd61cc6e4943a4fda96
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87459260"
---
# <a name="view-azure-reservations"></a>查看 Azure 预留项

可以在 Azure 门户中查看和管理购买的预留项。

## <a name="permissions-to-view-a-reservation"></a>预留项的查看权限

若要查看或管理预留项，需要对其拥有读取者或所有者权限。 默认情况下，当你购买某个预留项时，你和帐户管理员自动获得预留订单和该预留项的“所有者”角色。 若要允许其他人查看预留，必须将其添加为预留订单或预留的“所有者”或“读者”。 将某人添加到提供用于计收预留费用的订阅不会自动将此人添加到预留项。 

有关详细信息，请参阅[添加或更改可以管理预留的用户](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>在 Azure 门户中查看预留和利用率

若要以所有者或读取者身份查看预留，请执行以下操作

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 转到[预留项](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
3. 该列表将显示你对其具有“所有者”或“读者”角色的所有预留项。 每个预留显示最后已知的利用率百分比。
4. 单击利用率百分比查看使用历史记录和详细信息。 请查看以下视频中的详细信息。
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>使用 API、PowerShell、CLI 获取预留项和利用率

使用以下资源获取所有预留项的列表
- [API：预留订单 - 列表](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell：预留订单 - 列表](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI：预留订单 - 列表](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

还可以使用预留实例使用情况 API 获取[预留项利用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)。 

## <a name="see-reservations-and-utilization-in-power-bi"></a>在 Power BI 中查看预留项和利用率

Power BI 用户可使用两个选项
- 内容包：预留项购买和利用率数据在 [Consumption Insights Power BI 内容包](/power-bi/desktop-connect-azure-cost-management)中提供。 可使用此内容包创建所需的报表。 
- 成本管理应用：使用[成本管理应用](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)获取可以进一步自定义的预创建报表。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 预留项](manage-reserved-vm-instance.md)。
- [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的预留使用情况](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
