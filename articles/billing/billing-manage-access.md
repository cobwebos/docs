---
title: 管理对 Azure 账单信息的访问权限 | Microsoft Docs
description: 了解如何向团队成员提供对 Azure 帐单信息的访问权限。
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491071"
---
# <a name="manage-access-to-billing-information-for-azure"></a>管理对 Azure 账单信息的访问权限

您可以为您的帐户在 Azure 门户中提供其他人访问计费信息。 计费角色类型和提供访问计费信息的说明因您的计费帐户的类型而异。 若要确定您的计费帐户的类型，请参阅[检查您的计费帐户类型](#check-the-type-of-your-billing-account)。

本文适用于 Microsoft Online Service 程序帐户的客户。 如果你是具有企业协议 (EA) 的 Azure 客户，并且是企业管理员，您可以在企业门户中向部门管理员和帐户所有者授予权限。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。 如果你是 Microsoft 客户协议客户，请参阅，[了解 Microsoft 客户协议在 Azure 中的管理角色](billing-understand-mca-roles.md)。 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Microsoft Online Service 程序帐户的帐户管理员

帐户管理员是 Microsoft 在线服务计划的计费帐户的唯一所有者。 将角色分配给注册了 Azure 的人员。 帐户管理员有权执行各种计费任务，例如创建订阅，查看发票或更改订阅的计费。

## <a name="give-others-access-to-view-billing-information"></a>授予其他人查看计费信息的访问权限

帐户管理员可以授予其他人对 Azure 计费信息的访问通过分配其帐户中的订阅上的以下角色之一。

- 服务管理员
- 共同管理员
- 所有者
- 参与者
- 读取器
- 计费读者

这些角色有权访问中的计费信息[Azure 门户](https://portal.azure.com/)。 分配这些角色的用户还可以使用[计费 Api](billing-usage-rate-card-overview.md)以编程方式获取发票和使用情况详细信息。

若要分配角色，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

\* * 如果你是 EA 客户，帐户所有者可以将上面的角色分配给他们的团队的其他用户。 但是，对于这些用户能够查看计费信息，企业管理员必须启用 AO 查看费用在 Enterprise portal 中。


### <a name="opt-in"></a> 允许用户下载发票

帐户管理员已向其他用户分配相应的角色后，它们必须开启下载发票，在 Azure 门户中的访问权限。 早于 2016 年 12 月的发票仅供帐户管理员查看。

1. 登录到[Azure 门户](https://portal.azure.com/)，为帐户管理员、

1. 在“成本管理 + 计费”  中进行搜索。

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. 选择**订阅**从左侧窗格。 根据您的访问权限，可能需要选择计费的作用域，然后选择**订阅**。
 
    ![屏幕截图，显示选择的订阅](./media/billing-manage-access/billing-select-subscriptions.png)

1. 选择**发票**，然后**访问发票**。

    ![显示如何委托对发票的访问权限的屏幕截图](./media/billing-manage-access/AA-optin.png)

1. 选择“启用”  ，然后进行保存。

    ![屏幕截图，显示用于委托对发票的访问权限的开关](./media/billing-manage-access/AA-optinAllow.png)

帐户管理员还可配置通过电子邮件发送发票。 若要了解详细信息，请参阅[在电子邮件中获取发票](billing-download-azure-invoice-daily-usage-date.md)。

## <a name="give-read-only-access-to-billing"></a>授予对账单信息的只读访问权限

将“账单读取者”角色分配给需要以只读方式访问订阅账单信息的人员，而不是需要 Azure 服务管理或创建功能的人员。 此角色适用于组织中负责 Azure 订阅的财务和成本管理的用户。

计费读取者功能处于预览状态，尚不支持非全局云。

1. 登录到[Azure 门户](https://portal.azure.com/)，为帐户管理员、

1. 在“成本管理 + 计费”  中进行搜索。

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. 选择**订阅**从左侧窗格。 根据您的访问权限，可能需要选择计费的作用域，然后选择**订阅**。
 
    ![屏幕截图，显示选择的订阅](./media/billing-manage-access/billing-select-subscriptions.png)

1. 选择“访问控制 (IAM)”  。
1. 选择**添加**从页面顶部。

    ![显示单击的屏幕截图添加角色分配](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. 在“角色”  下拉列表中，选择“账单读取者”  。
1. 在“选择”文本框中，键入要添加的用户的名称或电子邮件。 
1. 选择用户。
1. 选择“保存”。 
    ![显示单击的屏幕截图添加角色分配](./media/billing-manage-access/billing-save-role-assignment.png)

1. 几分钟后用户分配订阅的计费读取者角色。

\* * 如果你是 EA 客户，帐户所有者或部门管理员可以向团队成员分配计费读取者角色。 但是，企业管理员必须在企业门户中启用“AO 视图费用”或“DA 视图费用”策略，该账单读取者才能查看部门或帐户的账单信息。  

## <a name="check-the-type-of-your-billing-account"></a>检查您的计费帐户的类型
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>后续步骤

- 其他角色的用户（如所有者或参与者）不仅可访问计费信息，还可访问 Azure 服务。 若要管理这些角色，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。
- 有关角色的详细信息，请参阅 [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
