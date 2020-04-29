---
title: 多租户交互的特征 - Azure AD | Microsoft Docs
description: 了解作为完全独立组织的 Azure Active Directory 租户
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878113"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>了解多个 Azure Active Directory 组织的交互方式

在 Azure Active Directory （Azure AD）中，每个租户都是完全独立的组织：在逻辑上独立于你管理的其他 Azure AD 组织的对等方。 组织间的这种独立性包括资源独立性、管理独立性和同步独立性。 组织之间没有父子关系。

## <a name="resource-independence"></a>资源独立性

* 如果在一个组织中创建或删除 Azure AD 资源，则它不会影响另一组织中的任何资源，外部用户除外。
* 如果向一个组织注册某个域名，则其他任何组织都不能使用该域名。

## <a name="administrative-independence"></a>管理独立性

如果组织 "Contoso" 的非管理用户创建了测试组织的 "测试"，则：

* 默认情况下，创建组织的用户将添加为该新组织中的外部用户，并在该组织中分配全局管理员角色。
* 除非 "Test" 的管理员专门向其授予了这些特权，否则组织 "Contoso" 的管理员对组织 "Test" 没有直接管理权限。 但是，如果用户控制创建 "Test" 的用户帐户，则 "Contoso" 的管理员可以控制对组织 "Test" 的访问。
* 如果为一个组织中的用户添加或删除 Azure AD 角色，则更改不会影响该用户在任何其他 Azure AD 组织中分配的角色。

## <a name="synchronization-independence"></a>同步独立性

您可以单独配置每个 Azure AD 组织，以获取从以下任一实例同步的数据：

* Azure AD Connect 工具，用于将数据与一个 AD 林同步。
* 适用于 Forefront Identity Manager 的 Azure Active Directory 连接器，用于将数据与一个或多个本地林和/或非 Azure AD 数据源同步。

## <a name="add-an-azure-ad-organization"></a>添加 Azure AD 组织

若要在 Azure 门户中添加 Azure AD 组织，请使用 Azure AD 全局管理员帐户登录 Azure 门户，然后选择["](https://portal.azure.com) **新建**"。

> [!NOTE]
> 与其他 Azure 资源不同，Azure AD 组织不是 Azure 订阅的子资源。 如果你的 Azure 订阅已取消或过期，仍可使用 Azure PowerShell、Microsoft Graph API 或 Microsoft 365 管理中心访问 Azure AD 组织的数据。 你还可以[将另一个订阅与组织相关联](../fundamentals/active-directory-how-subscriptions-associated-directory.md)。
>

## <a name="next-steps"></a>后续步骤

有关 Azure AD 许可注意事项和最佳实践，请参阅[什么是 Azure Active Directory 许可？](../fundamentals/active-directory-licensing-whatis-azure-portal.md)。
