---
title: 管理 Azure 虚拟机预留实例 | Microsoft Docs
description: 了解如何为 Azure 虚拟机预留实例更改订阅范围以及管理访问权限。
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064296"
---
# <a name="manage-reserved-instances-in-azure"></a>管理 Azure 中的预留实例

购买 Azure 虚拟机预留实例后，可能需要将预留实例应用到与购买过程中指定的订阅不同的订阅。 或者，如果匹配的虚拟机正在多个订阅中运行，可能需要将预留实例范围更改为共享。 若要最大限度地增加预留实例折扣，请确保购买的实例数量与已在运行的虚拟机的属性和数量匹配。 若要了解有关 Azure 预留实例的详细信息，请参阅[通过为 Azure 虚拟机预付费节省资金](https://go.microsoft.com/fwlink/?linkid=862121)。

## <a name="change-the-scope-for-a-reserved-instance"></a>更改预留实例的范围
 预留实例折扣适用于与预留实例匹配并在预留实例范围内运行的虚拟机。 预留实例范围可以是计费上下文中的单个订阅或所有订阅。 如果将范围设置为单个订阅，预留实例将与所选订阅中正在运行的虚拟机匹配。 如果将范围设置为共享，Azure 会将预留实例与计费上下文内所有订阅中运行的虚拟机匹配。 计费上下文依赖于用于购买预留实例的订阅。 若要了解详细信息，请参阅[通过购买预留实例为 VM 预付费](https://go.microsoft.com/fwlink/?linkid=861721)。

若要更新预留实例范围，请执行以下操作： 
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务” > “预订”。
3. 选择预留实例。
4. 选择“设置” > “配置”。
5. 更改范围。 如果要从“共享”更改为单个范围，只能选择自己是其所有者的订阅。 只能选择与该预留实例相同的计费上下文中的订阅。 计费上下文取决于在购买预留实例时已选择的订阅。 范围仅适用于即用即付产品/服务 MS-AZR-0003P 订阅和 Enterprise 产品/服务 MS-AZR-0017P 订阅。 对于企业协议，开发/测试订阅不符合获取预留实例折扣的条件。

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>将一个预留实例拆分为两个预留实例
 购买多个实例后，可能需要将一个预留实例中的实例分配给其他订阅。 默认情况下，所有实例（在购买过程中指定的数量）都有一个范围 - 单个订阅或共享。 例如，已购买 10 个标准 D2 VM 并已将范围指定为订阅 A。现在可能需要将七个预留实例的范围更改为订阅 A，将剩余 3 个预留实例的范围更改为订阅 B。通过拆分预留实例可以分配实例以进行精细的范围管理。 可以通过选择共享范围来简化分配到订阅。 但出于成本管理或预算目的，可以将数量分配到特定订阅。

 可以通过 PowerShell、CLI 或通过 API 将一个预留实例拆分为两个预留实例。

### <a name="split-a-reserved-instance-by-using-powershell"></a>使用 PowerShell 拆分预留实例
1. 通过运行以下命令获取预留实例订单 ID：

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. 获取预留实例的详细信息：

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. 将该预留实例拆分为两个实例并对这两个实例进行分配：

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. 可运行以下命令更新范围：

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>添加或更改可以管理预留实例的用户
可以通过将用户添加到预留实例的角色来委托预留实例管理。 默认情况下，购买了预留实例的人员和帐户管理员有该预留实例的“所有者”角色。 

可以独立于获取预留实例折扣的订阅来管理对预留实例的访问权限。 如果为某人提供管理预留实例的权限，这不会向其提供管理订阅的权限。 如果为某人提供管理预留实例范围内的订阅的权限，这不会向其提供管理预留实例的权限。
 
若要委托预留实例的访问管理，请执行以下操作： 
1.  登录到 [Azure 门户](https://portal.azure.com)。
2.  选择“所有服务” > “预留”以列出自己有权访问的预留实例。
3.  选择要将访问权限委托给其他用户的预留实例。
4.  选择菜单中的“访问控制(IAM)”。
5.  选择“添加” > “角色” > “所有者”（或其他角色，如果希望提供有限的访问权限）。 
6. 键入要添加为“所有者”的用户的电子邮件地址。 
7. 选择用户，再选择“保存”。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 预留实例的详细信息，请参阅以下文章：

- [什么是 Azure 虚拟机预留实例？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [了解如何应用预留实例折扣](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留实例使用情况](billing-understand-reserved-instance-usage.md)
- [了解适用于企业合约的预留实例使用情况](billing-understand-reserved-instance-usage-ea.md)
- [预留实例未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
