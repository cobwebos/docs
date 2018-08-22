---
title: 管理 Azure 预留 | Microsoft Docs
description: 了解如何为 Azure 预留更改订阅范围以及管理访问权限。
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
ms.date: 08/08/2018
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628566"
---
# <a name="manage-reservations-for-resources-in-azure"></a>管理 Azure 中资源的预留

购买 Azure 预留后，可能需要将预留应用到与在购买过程中指定的订阅不同的订阅。 或者，如果匹配的虚拟机、SQL 数据库或其他资源正在多个订阅中运行，可能需将预留范围更改为共享。 若要最大限度地增加预留折扣，请确保购买的实例数量与已在运行的资源的属性和数量匹配。 若要了解详细信息，请参阅 [Azure 预留](https://go.microsoft.com/fwlink/?linkid=862121)。

## <a name="change-the-scope-for-a-reservation"></a>更改预订范围

 预留折扣适用于与预留匹配并在预留范围内运行的虚拟机、SQL 数据库或其他资源。 预订范围可以是计费上下文中的单个订阅或所有订阅。 如果将范围设置为单个订阅，预留将与所选订阅中正在运行的资源匹配。 如果将范围设置为共享，Azure 会将预留与计费上下文内所有订阅中运行的资源匹配。 计费上下文依赖于用于购买预订的订阅。

若要更新预订范围，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择该预订。
4. 选择“设置” > “配置”。
5. 更改范围。 如果要从“共享”更改为单个范围，只能选择自己是其所有者的订阅。 只能选择与该预订相同的计费上下文中的订阅。 计费上下文取决于在购买预订时已选择的订阅。 范围仅适用于即用即付套餐 MS-AZR-0003P 订阅和 Enterprise 套餐 MS-AZR-0017P 订阅。 对于企业协议，开发/测试订阅不符合获取预订折扣的条件。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>添加或更改可以管理预订的用户

可以通过将用户添加到预订的角色来委托预订管理。 默认情况下，购买了预订的人员和帐户管理员有该预订的“所有者”角色。

可以独立于获取预订折扣的订阅来管理对预订的访问权限。 如果为某人提供管理预订的权限，这不会向其提供管理订阅的权限。 如果为某人提供管理预订范围内的订阅的权限，这不会向其提供管理预订的权限。

若要委托预订的访问管理，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”以列出自己有权访问的预订。
3. 选择要将访问权限委托给其他用户的预订。
4. 选择菜单中的“访问控制(IAM)”。
5. 选择“添加” > “角色” > “所有者”（或其他角色，如果希望提供有限的访问权限）。
6. 键入要添加为“所有者”的用户的电子邮件地址。 
7. 选择用户，再选择“保存”。

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>优化虚拟机预留实例以确保 VM 大小灵活性或容量优先级

 VM 实例可以灵活调整，因此可将预留折扣应用到同一 [VM 大小组](https://aka.ms/RIVMGroups)中的其他 VM。 默认情况下，在共享预留范围时，实例大小灵活性处于启用状态，数据中心容量不会根据 VM 部署来确定优先级。 对于只有一个范围的预留，可以根据容量优先级而不是 VM 实例大小灵活性来优化预留。 容量优先级将为你的部署预留数据中心容量，使你在需要时能够更加有把握地启动虚拟机实例。

若要更新预订范围，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择该预订。
4. 选择“设置” > “配置”。
5. 根据设置更改“优化”。

## <a name="split-a-single-reservation-into-two-reservations"></a>将一个预订拆分为两个预订

 购买多个实例后，可能会希望将一个预订中的实例分配给其他订阅。 默认情况下，所有实例（在购买过程中指定的数量）都有一个范围 - 单个订阅或共享。 例如，你已购买 10 个标准 D2 VM 并将范围指定为订阅 A。现在可能需要将七个预留的范围更改为订阅 A，将剩余 3 个预留范围更改为订阅 B。通过拆分预留可以分配实例以进行精细的范围管理。 可以通过选择共享范围来简化分配到订阅。 但出于成本管理或预算目的，可以将数量分配到特定订阅。

 可以通过 PowerShell、CLI 或通过 API 将一个预订拆分为两个预订。

### <a name="split-a-reservation-by-using-powershell"></a>使用 PowerShell 拆分预订

1. 通过运行以下命令获取预订订单 ID：

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. 获取预订的详细信息：

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 将该预订拆分为两个预订并分配实例：

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 可运行以下命令更新范围：
    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预留的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库预留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [了解如何应用预留折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
