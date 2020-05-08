---
title: 多租户交互的特征 - Azure AD | Microsoft Docs
description: 了解 Azure Active Directory 组织的数据独立性
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe7b0211775e59504443d30fa253cfa14af13b1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582742"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>了解多个 Azure Active Directory 组织如何交互

在 Azure Active Directory （Azure AD）中，每个 Azure AD 组织都是完全独立的：对等方，在逻辑上独立于你管理的其他 Azure AD 组织。 组织之间的这种独立性包括资源独立性、管理独立性和同步独立性。 组织之间不存在父子关系。

## <a name="resource-independence"></a>资源独立性

* 如果在一个组织中创建或删除了 Azure AD 资源，这不影响另一个组织中的任何资源，但对于外部用户来说，情况并非完全如此。
* 如果向一个组织注册了某个域名，则任何其他组织都不能使用该域名。

## <a name="administrative-independence"></a>管理独立性

如果组织“Contoso”的某个非管理用户创建了测试组织“Test”，那么：

* 默认情况下，会在该新组织中将创建组织的用户添加为外部用户，并在该组织中为其分配全局管理员角色。
* 组织“Contoso”的管理员对组织“Test”没有直接管理特权，除非“Test”的管理员专门向其授予了这些特权。 不过，如果“Contoso”的管理员控制创建“Test”的用户帐户，则可以控制对组织“Test”的访问。
* 如果为一个组织中的用户添加或删除 Azure AD 角色，则此更改不会影响在任何其他 Azure AD 组织中为该用户分配的角色。

## <a name="synchronization-independence"></a>同步独立性

可以独立配置每个 Azure AD 组织，以便通过下列任一工具的单个实例同步数据：

* Azure AD Connect 工具，用于将数据与一个 AD 林同步。
* 适用于 Forefront Identity Manager 的 Azure Active Directory 连接器，用于将数据与一个或多个本地林和/或非 Azure AD 数据源同步。

## <a name="add-an-azure-ad-organization"></a>添加 Azure AD 组织

若要在 Azure 门户中添加 Azure AD 组织，请使用作为 Azure AD 全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)，然后选择“新建”  。

> [!NOTE]
> 与其他 Azure 资源不同，你的 Azure AD 组织不是 Azure 订阅的子资源。 如果 Azure 订阅已取消或已过期，你仍可以使用 Azure PowerShell、Microsoft Graph API 或 Microsoft 365 管理中心来访问你的 Azure AD 组织的数据。 还可以[将其他订阅与组织相关联](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
>

## <a name="next-steps"></a>后续步骤

有关 Azure AD 许可注意事项和最佳做法，请参阅 [什么是 Azure Active Directory 许可？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)。
