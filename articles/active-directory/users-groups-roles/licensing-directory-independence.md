---
title: 多个租户交互的特征 - Azure Active Directory | Microsoft Docs
description: 通过将租户视为完全独立的资源，管理 Azure Active 租户
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45f48b6d8ef29d14606f18d4ccee77bd742a670a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60470066"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>了解多个 Azure Active Directory 租户的交互方式

在 Azure Active Directory (Azure AD) 中，每个租户都是完全独立的资源：也就是说，对等租户与管理的其他租户在逻辑上相互独立。 租户之间不存在父子关系。 租户的这种独立性表现为资源独立性、管理独立性和同步独立性。

## <a name="resource-independence"></a>资源独立性
* 在一个租户中创建或删除某资源不影响其他租户中的任何资源，外部用户的部分例外情况除外。 
* 将域名之一用于一个租户后，它就不能用于其他任何租户了。

## <a name="administrative-independence"></a>管理独立性
如果租户“Contoso”的非管理用户创建了测试租户“Test”，那么：

* 默认情况下，创建租户的用户会在新租户中被添加为外部用户，并在此租户中分配有全局管理员角色。
* 租户“Contoso”管理员对租户“Test”没有直接管理权限，除非“Test”的管理员专门向他们授予这些权限。 不过，如果“Contoso”租户管理员控制创建“Test”租户的用户帐户，就可以控制对租户“Test”的访问。
* 如果在一个租户中添加/删除用户的管理员角色，此更改不会影响用户在另一个租户中所具有的管理员角色。

## <a name="synchronization-independence"></a>同步独立性
可以单独配置每个 Azure AD 租户，以便通过下列任意工具的单实例同步数据：

* Azure AD Connect 工具，用于将数据与一个 AD 林同步。
* 适用于 Forefront Identity Manager 的 Azure Active 租户连接器，用于将数据与一个或多个本地林和/或非 Azure AD 数据源同步。

## <a name="add-an-azure-ad-tenant"></a>添加 Azure AD 租户
若要在 Azure 门户中添加 Azure AD 租户，请使用作为 Azure AD 全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)，然后选择左侧的“新建”。

> [!NOTE]
> 与其他 Azure 资源不同，租户不是 Azure 订阅的子资源。 如果你的 Azure 订阅取消或已过期，仍可以访问你使用 Azure PowerShell、 Azure 图形 API 或 Microsoft 365 管理中心内的租户数据。 此外，还可以[将其他订阅与租户相关联](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
>

## <a name="next-steps"></a>后续步骤
有关 Azure AD 许可问题和最佳做法的简要介绍，请参阅[什么是 Azure Active 租户许可？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)。
