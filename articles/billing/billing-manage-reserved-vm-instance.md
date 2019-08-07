---
title: 管理 Azure 预订
description: 了解如何管理 Azure 保留项。
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
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840048"
---
# <a name="manage-reservations-for-azure-resources"></a>管理 Azure 资源的预留

购买 Azure 保留后, 你可能需要将预订应用于不同的订阅、更改可管理预订的人员或更改预订的作用域。 还可以将预留拆分为两个预留，以将购买的一些实例应用于另一个订阅。

如果已购买 Azure 保留虚拟机实例, 则可以更改保留的优化设置。 预留折扣可以应用于同一系列中的 VM，也可以为特定的 VM 大小保留数据中心容量。 而且, 您应该尝试优化预留, 以使它们完全使用。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>预订订单和预订

购买预订时, 会创建两个对象:**预订订单**和**预留**。

在购买时, 预订订单下面有一个预订。 拆分、合并、部分退款或 exchange 等操作按照**预订顺序**创建新预订。

若要查看预订订单, 请前往**预订**> 选择预订, 然后单击**预订订单 ID**。

![显示预订订单 ID 的预订订单详细信息示例 ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

预订从其预订顺序继承权限。

## <a name="change-the-reservation-scope"></a>更改保留范围

 你的预订折扣适用于虚拟机、SQL 数据库、Azure Cosmos DB 或其他资源, 这些资源匹配你的预订并在保留范围中运行。 计费上下文依赖于用于购买预订的订阅。

若要更新预订范围，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择该预订。
4. 选择“设置” > “配置”。
5. 更改范围。

如果从 "共享" 更改为 "单个作用域", 则只能选择你是其所有者的订阅。 只能选择与该预订相同的计费上下文中的订阅。

此范围仅适用于按现用现付费率的各个订阅 (提供 BC-OP-NT-AZR-Ms-azr-0003p 或 BC-OP-NT-AZR-0023P)、Enterprise 提供 MS-BC-OP-NT-AZR-Ms-azr-0017p 或 MS-bc-op-nt-azr-ms-azr-0148p 或 CSP 订阅类型。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>添加或更改可以管理预订的用户

可以通过将人员添加到预订订单或预订的角色来委托预留管理。 默认情况下, 放置预订订单和帐户管理员的人员具有预订订单和预订的所有者角色。

您可以独立于获得预订折扣的订阅管理对预订订单和预订的访问。 当你向某人授予管理预订订单或预订的权限时, 它不会向他们授予管理订阅的权限。 同样, 如果你向某人授予了在保留范围中管理订阅的权限, 则不会向他们授予管理预订订单或预订的权限。

若要执行交换或退款, 用户必须对预订订单具有访问权限。 向某人授予权限时, 最好是将权限授予预订顺序, 而不是保留。


若要委托预订的访问管理，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”以列出自己有权访问的预订。
3. 选择要将访问权限委托给其他用户的预订。
4. 选择“访问控制 (IAM)”。
5. 选择“添加角色分配” > “角色” > “所有者”。 或者，如果希望提供有限的访问权限，请选择其他角色。
6. 键入要添加为“所有者”的用户的电子邮件地址。
7. 选择用户，再选择“保存”。

## <a name="split-a-single-reservation-into-two-reservations"></a>将一个预订拆分为两个预订

 购买一个预留中的多个资源实例后，可能会希望将一个预留中的实例分配给其他订阅。 默认情况下，所有实例都有一个范围 - 单个订阅或共享。 例如, 你购买了10个预订实例, 并指定了要作为订阅 A 的作用域。你现在可能想要将7个预订的作用域更改为订阅 A, 并将其余三个预订更改为订阅 B。通过拆分保留, 你可以为粒度范围管理分配实例。 可以通过选择共享范围来简化分配到订阅。 但出于成本管理或预算目的，可以将数量分配到特定订阅。

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

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交换或退款预订

您可以取消、交换或退款保留, 但有一些限制。 有关详细信息, 请参阅[Azure 预订的自助服务交换和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>更改预留 VM 实例的优化设置

 购买 VM 预留实例时，可以选择实例大小的灵活性或容量优先级。 实例大小可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](https://aka.ms/RIVMGroups)中的其他 VM。 容量优先级可以对部署优先使用数据中心容量。 此选项使你在需要时能够更加有把握地启动 VM 实例。

默认情况下，在共享预留范围时，将启用实例大小灵活性。 数据中心容量未针对 VM 部署设置优先级。

对于只有一个范围的预留，可以根据容量优先级而不是 VM 实例大小灵活性来优化预留。

若要更新预留的优化设置，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择该预订。
4. 选择“设置” > “配置”。
5. 更改“优化对象”设置。

## <a name="optimize-reservation-use"></a>优化保留使用

Azure 保留节省仅因资源使用持续而导致。 当你进行预订购买时, 可以在一年或三年的时间内为资源使用的可能资源使用情况提前支付一项100成本。 尝试最大限度地提高您的预订量, 以尽可能多地使用和节省费用。 以下部分说明如何监视保留并优化其使用。

### <a name="view-reservation-use-in-the-azure-portal"></a>在 Azure 门户中查看预订使用情况

查看保留使用情况的一种方法是在 Azure 门户中。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 "**所有服务** > [**保留**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)" 并记下预留的**利用率 (%)** 。  
  ![显示预订列表的图像](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. 选择预订。
4. 查看随时间推移使用的保留趋势。  
  ![显示保留使用的图像 ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>使用 API 查看保留使用情况

如果你是企业协议 (EA) 客户, 则可以通过编程方式查看你的组织中的预订的使用方式。 通过使用情况数据获取未使用的预订。 当您查看预订费用时, 请记住, 数据划分为实际成本和摊销成本。 实际成本提供数据来协调每月帐单。 它还具有预订采购成本和预订应用程序详细信息。 摊销成本与实际成本类似, 只不过预留用量的有效价格为按比例分配。 未使用的预订时间显示在摊销成本数据中。 有关 EA 客户的使用情况数据的详细信息, 请参阅[获取企业协议预订成本和使用情况](billing-understand-reserved-instance-usage-ea.md)。

对于其他订阅类型, 请使用 "API[预订摘要"-按预订订单和预订列出](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)。

### <a name="optimize-your-reservation"></a>优化你的预订

如果你发现组织的预订正在被充分利用:

- 请确保你的组织创建的虚拟机与保留的 VM 大小相匹配。
- 确保已启用实例大小灵活性。 有关详细信息，请参阅[管理预留 - 更改预留 VM 实例的优化设置](#change-optimize-setting-for-reserved-vm-instances)。
- 将预订的作用域更改为 "_共享_", 使其更广泛地应用。 有关详细信息, 请参阅[更改保留范围](#change-the-reservation-scope)。
- 请考虑交换未使用的数量。 有关详细信息, 请参阅[取消和交换](#cancel-exchange-or-refund-reservations)。


## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [Azure 的保留内容是什么？](billing-save-compute-costs-reservations.md)

购买服务计划:
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [通过 Azure Cosmos DB 保留容量预付 Azure Cosmos DB 资源费用](../cosmos-db/cosmos-db-reserved-capacity.md)

购买软件计划:
- [预付 Azure 预订的 Red Hat 软件计划](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [通过 Azure 预留为 SUSE 软件计划预付费](../virtual-machines/linux/prepay-suse-software-charges.md)

了解折扣和使用情况:
- [了解 VM 预留折扣的应用方式](billing-understand-vm-reservation-charges.md)
- [了解如何应用 Red Hat Enterprise Linux 软件计划折扣](../billing/billing-understand-rhel-reservation-charges.md)
- [了解如何应用 SUSE Linux Enterprise 软件计划折扣](../billing/billing-understand-suse-reservation-charges.md)
- [了解如何应用其他预留折扣](billing-understand-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预订未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
