---
title: 管理 Azure 预留
description: 了解如何管理 Azure 预留。
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "68840048"
---
# <a name="manage-reservations-for-azure-resources"></a>管理 Azure 资源的预留

购买 Azure 预留后，可能需要将预留应用到其他订阅，更改可以管理预留的人员，或更改预留的范围。 还可以将预留拆分为两个预留，以将购买的一些实例应用于另一个订阅。

如果购买了 Azure 虚拟机预留实例，则可以更改预留的优化设置。 预留折扣可以应用于同一系列中的 VM，也可以为特定的 VM 大小保留数据中心容量。 另外，你应该尝试优化预留，使之充分发挥作用。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>预留订单和预留

购买预留时，会创建两个对象：**预留订单**和**预留**。

在购买时，预留订单下有一个预留。 拆分、合并、部分退款或交换之类的操作会在“预留订单”下创建新的预留。 

若要查看预留订单，请转到“预留”，  选择预留，然后单击“预留订单 ID”。 

![预留订单详细信息的示例，显示预留订单 ID ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

预留从其预留订单继承权限。

## <a name="change-the-reservation-scope"></a>更改预留范围

 预留折扣适用于与预留匹配并在预留范围内运行的虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源。 计费上下文依赖于用于购买预订的订阅。

若要更新预订范围，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”   > “预订”  。
3. 选择该预订。
4. 选择“设置”   > “配置”  。
5. 更改范围。

如果要从“共享”更改为单个范围，只能选择自己是其所有者的订阅。 只能选择与该预订相同的计费上下文中的订阅。

此范围仅适用于使用即用即付费率的单个订阅（套餐 MS-AZR-0003P 或 MS-AZR-0023P），企业套餐 MS-AZR-0017P 或 MS-AZR-0148P，或 CSP 订阅类型。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>添加或更改可以管理预订的用户

可以通过将用户添加到预留订单或预留的角色来委托预留管理。 默认情况下，下预留订单的人员和帐户管理员拥有该预留订单或预留的所有者角色。

可以独立于获取预留折扣的订阅来管理对预留订单和预留的访问权限。 为某人提供管理预留订单或预留的权限时，不会向其提供管理订阅的权限。 同样，为某人提供管理预留范围内的订阅的权限时，不会向其提供管理预留订单或预留的权限。

若要执行交换或退款操作，用户必须有权访问预留订单。 为某人授予权限时，最好是授予预留订单的权限，而不是预留的权限。


若要委托预订的访问管理，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”   > “预订”  以列出自己有权访问的预订。
3. 选择要将访问权限委托给其他用户的预订。
4. 选择“访问控制 (IAM)”  。
5. 选择“添加角色分配”   > “角色”   > “所有者”  。 或者，如果希望提供有限的访问权限，请选择其他角色。
6. 键入要添加为“所有者”的用户的电子邮件地址。
7. 选择用户，再选择“保存”  。

## <a name="split-a-single-reservation-into-two-reservations"></a>将一个预订拆分为两个预订

 购买一个预留中的多个资源实例后，可能会希望将一个预留中的实例分配给其他订阅。 默认情况下，所有实例都有一个范围 - 单个订阅或共享。 例如，已购买 10 个预留实例并将范围指定为订阅 A。现在可能需要将 7 个预留的范围更改为订阅 A，将剩余 3 个预留范围更改为订阅 B。通过拆分预留可以分配实例以进行精细的范围管理。 可以通过选择共享范围来简化分配到订阅。 但出于成本管理或预算目的，可以将数量分配到特定订阅。

 可以通过 PowerShell、CLI 或通过 API 将一个预订拆分为两个预订。

### <a name="split-a-reservation-by-using-powershell"></a>使用 PowerShell 拆分预订

1. 通过运行以下命令获取预订订单 ID：

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 获取预订的详细信息：

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 将该预订拆分为两个预订并分配实例：

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 可运行以下命令更新范围：

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>更改预留 VM 实例的优化设置

 购买 VM 预留实例时，可以选择实例大小的灵活性或容量优先级。 实例大小可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](https://aka.ms/RIVMGroups)中的其他 VM。 容量优先级可以对部署优先使用数据中心容量。 此选项使你在需要时能够更加有把握地启动 VM 实例。

默认情况下，在共享预留范围时，将启用实例大小灵活性。 数据中心容量未针对 VM 部署设置优先级。

对于只有一个范围的预留，可以根据容量优先级而不是 VM 实例大小灵活性来优化预留。

若要更新预留的优化设置，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”   > “预订”  。
3. 选择该预订。
4. 选择“设置”   > “配置”  。
5. 更改“优化对象”  设置。

## <a name="optimize-reservation-use"></a>优化预留的使用

只有持续使用资源，才会有 Azure 预留节省好处。 进行预留购买时，你提前支付一笔费用，获得的是在一到三年的期限内几乎 100% 可能的资源使用。 尝试将预留最大化，以获取尽可能多的使用和节省。 下面各部分介绍了如何监视预留并优化其使用。

### <a name="view-reservation-use-in-the-azure-portal"></a>在 Azure 门户中查看预留使用

查看预留使用的一种方式是使用 Azure 门户。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“所有服务” > [ **“预留”** ](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)，记下某个预留的“利用率(%)”。    
  ![显示预留列表的图像](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. 选择一个预留。
4. 查看一段时间的预留使用趋势。  
  ![显示预留使用的图像 ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>通过 API 查看预留使用

如果你是企业协议 (EA) 客户，则可以编程方式查看组织中的预留是如何使用的。 通过使用数据获取未使用的预留 查看预留费用时，请记住，数据按实际成本和摊销成本划分。 实际成本提供要与每月账单对帐的数据。 它还包括预留项购买成本和预留项应用详细信息。 摊销成本类似于实际成本，只是预留使用量的实际价格按比例计算。 未使用的预留小时数显示在摊销成本数据中。 有关 EA 客户使用数据的详细信息，请参阅[获取企业协议预留成本和使用情况](billing-understand-reserved-instance-usage-ea.md)。

对于其他订阅类型，请使用 API [预览摘要 - 按预留订单和预留列出](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

### <a name="optimize-your-reservation"></a>优化预留

如果发现组织的预留未充分使用，请执行以下操作：

- 确保组织创建的虚拟机数与预留的 VM 大小相匹配。
- 确保已启用实例大小灵活性。 有关详细信息，请参阅[管理预留 - 更改预留 VM 实例的优化设置](#change-optimize-setting-for-reserved-vm-instances)。
- 将预留范围更改为“共享”，使其应用得更广。  有关详细信息，请参阅[更改预留的范围](#change-the-reservation-scope)。
- 考虑交换未使用的数量。 有关详细信息，请参阅[取消和交换](#cancel-exchange-or-refund-reservations)。


## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](billing-save-compute-costs-reservations.md)

购买服务计划：
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [通过 Azure Cosmos DB 保留容量预付 Azure Cosmos DB 资源费用](../cosmos-db/cosmos-db-reserved-capacity.md)

购买软件计划：
- [通过 Azure 预留为 Red Hat 软件计划预付费](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [通过 Azure 预留为 SUSE 软件计划预付费](../virtual-machines/linux/prepay-suse-software-charges.md)

了解折扣和使用情况：
- [了解 VM 预留折扣的应用方式](billing-understand-vm-reservation-charges.md)
- [了解 Red Hat Enterprise Linux 软件计划折扣是如何应用的](../billing/billing-understand-rhel-reservation-charges.md)
- [了解如何应用 SUSE Linux Enterprise 软件计划折扣](../billing/billing-understand-suse-reservation-charges.md)
- [了解如何应用其他预留折扣](billing-understand-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预订未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
