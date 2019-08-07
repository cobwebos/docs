---
title: 委托应用程序管理员创建和管理权限-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 中授予应用程序访问管理的权限
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e15fa8c79663fc2517039124f9be8c1ecd57b8a8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837881"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>在 Azure Active Directory 中委派应用注册权限

本文介绍如何在 Azure Active Directory (Azure AD) 中的自定义角色中使用应用权限来满足你的应用程序管理需求。 Azure Active Directory (Azure AD) 可通过以下方式委托应用程序的创建和管理权限:

- [限制可以创建应用程序的用户](#restrict-who-can-create-applications)和管理他们创建的应用程序。 默认情况下, 在 Azure AD 中, 所有用户都可以注册应用程序注册并管理他们创建的应用程序的所有方面。 这可以限制为仅允许所选人员具有权限。
- [将一个或多个所有者分配给应用程序](#assign-application-owners)。 这是一种简单的方法, 可让用户对特定应用程序的 Azure AD 配置的所有方面进行管理。
- [分配内置管理角色](#assign-built-in-application-admin-roles), 此角色授予对所有应用程序的 Azure AD 中的管理配置的访问权限。 这是向 IT 专家授予管理广泛的应用程序配置权限的建议方法, 而不授予管理与应用程序配置相关的 Azure AD 的其他部分的访问权限。
- [创建自定义角色](#create-and-assign-a-custom-role-preview), 定义非常具体的权限, 并将其分配给某人作为有限所有者的单个应用程序的作用域, 或者将其分配给受限制的管理员。

由于两个原因, 请务必考虑使用上述方法之一授予访问权限。 首先, 委派执行管理任务的功能可降低全局管理员开销。 其次, 使用有限权限可以提高安全状况, 并减少未经授权的访问的可能性。 [在 Azure Active Directory 中委托管理权限](roles-concept-delegation.md)中介绍了委托问题和一般准则。

## <a name="restrict-who-can-create-applications"></a>限制可以创建应用程序的人员

默认情况下, 在 Azure AD 中, 所有用户都可以注册应用程序注册并管理他们创建的应用程序的所有方面。 每个人还可以同意代表用户访问公司数据。 你可以选择通过将全局开关设置为 "否" 来有选择地授予这些权限, 并将所选用户添加到应用程序开发人员角色。

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>禁用创建应用程序注册或同意应用程序的默认功能

1. 使用符合 Azure AD 组织中全局管理员角色的帐户登录到 Azure AD 组织。
1. 拥有足够权限后，执行以下一个或两个设置：

    - 在组织的 " [用户设置" 页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)上, 将 "**用户可以注册应用程序**" 设置设置为 "否"。 这将禁用用户创建应用程序注册的默认功能。
    - 在 " [企业应用程序的用户设置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)" 中, 将 "**用户可以同意应用程序访问公司数据"** 设置为 "否"。 这将禁用用户同意代表他们访问公司数据的应用程序的默认功能。

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>当默认功能处于禁用状态时, 授予单独的权限以创建和同意应用程序

分配应用程序开发人员角色, 以便在 "**用户可以注册应用程序**" 设置设置为 "否" 时授予创建应用程序注册的能力。 如果**用户可以同意应用代表他们访问公司数据的应用**将设置为 "否", 则此角色还会代表自己授予许可。 作为系统行为, 当用户创建新的应用程序注册时, 它们会自动添加为第一个所有者。 所有权权限使用户能够管理应用程序注册的所有方面或其拥有的企业应用程序。

## <a name="assign-application-owners"></a>分配应用程序所有者

分配所有者是一种简单的方法, 可用于为特定应用程序注册或企业应用程序管理 Azure AD 配置的所有方面。 作为系统行为, 当用户创建新的应用程序注册时, 它们会自动添加为第一个所有者。 所有权权限使用户能够管理应用程序注册的所有方面或其拥有的企业应用程序。 可以删除原始所有者, 还可以添加其他所有者。

### <a name="enterprise-application-owners"></a>企业应用程序所有者

作为所有者, 用户可以管理企业应用程序的组织特定配置, 例如单一登录配置、设置和用户分配。 所有者还可以添加或删除其他所有者。 与全局管理员不同, 所有者只能管理他们拥有的企业应用程序。

在某些情况下, 从应用程序库创建的企业应用程序包括企业应用程序和应用程序注册。 如果为 true, 则将所有者添加到企业应用程序会自动将所有者作为所有者添加到相应的应用程序注册。

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>向企业应用程序分配所有者

1. 使用有资格用于组织的应用程序管理员或云应用程序管理员的帐户登录到[Azure AD 组织](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
1. 在组织的 " [应用注册" 页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) 上, 选择一个应用以打开该应用的 "概述" 页。
1. 选择 " **所有者** ", 查看该应用的所有者列表。
1. 选择 " **添加** " 以选择一个或多个要添加到应用的所有者。

> [!IMPORTANT]
> 用户和服务主体可以是应用程序注册的所有者。 只有用户可以是企业应用程序的所有者。 不能将组分配为任何一个的所有者。
>
> 所有者可以向应用程序添加凭据, 并使用这些凭据来模拟应用程序的标识。 应用程序的权限可能比所有者多, 因此, 对所有者有权访问作为用户或服务主体的权限的权限提升。 当模拟应用程序时, 应用程序所有者可能会创建或更新用户或其他对象, 具体取决于应用程序的权限。

## <a name="assign-built-in-application-admin-roles"></a>分配内置的应用程序管理员角色

Azure AD 包含一组内置管理员角色, 用于授予访问权限, 以便对所有应用程序的 Azure AD 中的配置进行管理。 建议将这些角色授予 IT 专家管理广泛的应用程序配置权限的权限, 而无需授予管理与应用程序配置相关的 Azure AD 的其他部分的访问权限。

- 应用程序管理员:充当此角色的用户可以创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。
- 云应用程序管理员:充当角色的用户具有与应用程序管理员角色相同的权限，但不包括管理应用程序代理的权限。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

有关详细信息以及查看这些角色的说明, 请参阅 [可用角色](directory-assign-admin-roles.md#available-roles)。

按照为[用户分配角色 Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)操作方法指南中的说明分配应用程序管理员或云应用程序管理员角色。

> [!IMPORTANT]
> 应用程序管理员和云应用程序管理员可以向应用程序添加凭据, 并使用这些凭据来模拟应用程序的标识。 应用程序的权限可能是对管理员角色权限的提升权限。 当模拟应用程序时, 此角色中的管理员可能会创建或更新用户或其他对象, 具体取决于应用程序的权限。
> 任何一个角色都不授权管理“条件访问”设置。

## <a name="create-and-assign-a-custom-role-preview"></a>创建和分配自定义角色 (预览)

创建自定义角色和分配自定义角色是单独的步骤:

- [创建自定义*角色定义*](roles-create-custom.md) , 并[从预设列表中添加对其的权限](roles-custom-available-permissions.md)。 这些是在内置角色中使用的相同权限。
- [创建*角色分配*](roles-assign-powershell.md)以分配自定义角色。

这种分离使你可以创建单个角色定义, 然后在不同的*范围*内将其分配多次。 可以在组织范围的范围内分配自定义角色, 也可以在单个 Azure AD 对象的范围内分配自定义角色。 对象范围的一个示例是单个应用程序注册。 使用不同的作用域, 可以在组织中的所有应用注册上将同一角色定义分配给 Sally, 然后只 Naveen Contoso Expense 报表应用注册。

创建和使用自定义角色来委派应用程序管理时的技巧:
- 自定义角色仅在 Azure AD 门户的最新应用注册边栏选项卡中授予访问权限。 它们不会在旧的应用注册边栏选项卡中授予访问权限。
- 当 "限制对 Azure AD 管理门户的访问权限" 用户设置设置为 "是" 时, 自定义角色不会授予对 Azure AD 门户的访问权限。
- 应用注册用户有权使用角色分配, 则只会在 "应用注册" 页上的 "所有应用程序" 选项卡中显示。 它们不会显示在 "拥有的应用程序" 选项卡中。

有关自定义角色的基础知识的详细信息, 请参阅[自定义角色概述](roles-custom-overview.md), 以及如何[创建自定义角色](roles-create-custom.md)以及如何[分配角色](roles-assign-powershell.md)。

## <a name="next-steps"></a>后续步骤

- [应用程序注册子类型和权限](roles-custom-available-permissions.md)
- [Azure AD 管理员角色参考](directory-assign-admin-roles.md)
