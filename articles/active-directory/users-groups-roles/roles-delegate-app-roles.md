---
title: 委托应用程序管理管理 perms-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中授予应用程序访问管理权限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 461ee1fc73448f16ba68850d6137fb396c658abd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024858"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>在 Azure Active Directory 中委托应用注册权限

本文介绍如何使用 Azure Active Directory (Azure AD) 中的自定义角色授予的权限来满足应用程序管理需求。 在 Azure AD中，可以通过以下方式委托应用程序创建和管理权限：

- [限制谁可以创建应用程序](#restrict-who-can-create-applications)和管理他们创建的应用程序。 默认情况下，Azure AD 中的所有用户都可以注册应用程序，并全方面地管理他们创建的应用程序。 可将此权限限制为选定的人员。
- [将一个或多个所有者分配到应用程序](#assign-application-owners)。 这是向用户授予全方面管理特定应用程序的 Azure AD 配置的权限的一种简单方法。
- [分配内置管理角色](#assign-built-in-application-admin-roles)，以授予管理 Azure AD 中所有应用程序的配置的访问权限。 建议通过此方法向 IT 专家授予管理广泛应用程序配置权限的访问权限，此方法无需授予管理与应用程序配置无关的其他 Azure AD 部分的访问权限。
- [创建自定义角色](#create-and-assign-a-custom-role-preview)，定义非常具体的权限并在单个应用程序的范围将其分配给受限所有者，或者在目录（所有应用程序）范围将其分配给受限管理员。

出于以下两种原因，必须考虑使用上述方法之一授予访问权限。 首先，委托执行管理任务的权限可以减少全局管理员开销。 其次，使用受限权限可以改善安全态势，并减少未经授权访问的可能性。 [在 Azure Active Directory 中委托管理权限](roles-concept-delegation.md)中介绍了委托问题和一般准则。

## <a name="restrict-who-can-create-applications"></a>限制谁可以创建应用程序

默认情况下，Azure AD 中的所有用户都可以注册应用程序，并全方面地管理他们创建的应用程序。 每个用户还可以许可代表其访问公司数据的应用。 可以通过将全局开关设置为“否”并将选定用户添加到“应用程序开发人员”角色，有选择地授予这些权限。

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>禁用创建应用程序注册或许可应用程序的默认权限

1. 使用符合 Azure AD 组织中“全局管理员”角色条件的帐户登录到 Azure AD 组织。
1. 设置下列一项或两项：

    - 在 [组织的“用户设置”页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)上，将“用户可以注册应用程序”设置为“否”。 这会禁用用户用来创建应用程序注册的默认权限。
    - 在 [企业应用程序的用户设置](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)中，将“用户可以许可代表其访问公司数据的应用程序”设置为“否”。 这会禁用用户用来许可代表其访问公司数据的应用程序的默认权限。

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>禁用默认权限时授予创建和许可应用程序的单个权限

分配“应用程序开发人员”角色，以便在“用户可以注册应用程序”设置为“否”时，授予创建应用程序注册的权限。 当“用户可以许可代表其访问公司数据的应用”设置设为“否”时，此角色还能够代表自己授权许可权限。 作为一种系统行为，当用户创建新的应用程序注册时，他们将自动添加为第一个所有者。 所有权权限可让用户全方面管理其拥有的应用程序注册或企业应用程序。

## <a name="assign-application-owners"></a>分配应用程序所有者

分配所有者是授予全方面管理特定应用程序注册或企业应用程序的 Azure AD 配置权限的一种简单方法。 作为一种系统行为，当用户创建新的应用程序注册时，他们将自动添加为第一个所有者。 所有权权限可让用户全方面管理其拥有的应用程序注册或企业应用程序。 可以删除原始所有者，可以添加其他所有者。

### <a name="enterprise-application-owners"></a>企业应用程序所有者

作为所有者，用户可以管理企业应用程序的组织特定配置，例如单一登录配置、设置和用户分配。 所有者还可以添加或删除其他所有者。 与全局管理员不同，所有者只能管理他们拥有的企业应用程序。

在某些情况下，从应用程序库创建的企业应用程序包括企业应用程序和应用程序注册。 如果存在这种情况，则将某个所有者添加到企业应用程序会自动将该所有者添加为相应应用程序注册的所有者。

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>向企业应用程序分配所有者

1. 使用符合组织中应用管理员或云应用管理员条件的帐户登录到[](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)Azure AD 组织 。
1. 在组织的 [应用注册页](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)上，选择某个应用以打开该应用的“概述”页。 
1. 选择“所有者”，以查看应用所有者列表。
1. 选择“添加”，以选择一个或多个要添加到应用的所有者。

> [!IMPORTANT]
> 用户和服务主体可以是应用程序注册的所有者。 只有用户才能是企业应用程序的所有者。 无法将组分配为任何应用程序的所有者。
>
> 所有者可将凭据添加到应用程序，并使用这些凭据来模拟应用程序的标识。 应用程序拥有的权限可能多于所有者，因此，与身为用户或服务主体的所有者的访问权限相比，应用程序的特权将会提升。 在模拟应用程序时，根据应用程序的权限，应用程序所有者可能会创建或更新用户或其他对象。

## <a name="assign-built-in-application-admin-roles"></a>分配内置的应用程序管理员角色

Azure AD 提供一组内置的管理员角色，用于授予 Azure AD 中所有应用程序的配置管理访问权限。 建议使用这些角色向 IT 专家授予管理广泛应用程序配置权限的访问权限，这样就无需授予管理与应用程序配置无关的其他 Azure AD 部分的访问权限。

- 应用程序管理员：此角色中的用户可创建和管理企业应用程序、应用程序注册和应用程序代理设置的所有方面。 此角色还可以同意委派权限，以及除 Microsoft Graph 和 Azure AD Graph 之外的应用程序权限。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。
- 云应用程序管理员：此角色中的用户具有与应用程序管理员角色相同的权限，不包括管理应用程序代理的功能。 在创建新应用程序注册或企业应用程序时，不会将分配到此角色的用户添加为所有者。

有关详细信息以及如何查看这些角色的说明，请参阅 [可用的角色](directory-assign-admin-roles.md#available-roles)。

遵照[使用 Azure Active Directory 向用户分配角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)操作指南中的说明分配“应用程序管理员”或“云应用程序管理员”角色。

> [!IMPORTANT]
> 应用程序管理员和云应用程序管理员可向应用程序添加凭据，并使用这些凭据来模拟应用程序的标识。 与管理员角色的权限相比，应用程序的权限可能已提升。 在模拟应用程序时，根据应用程序的权限，充当此角色的管理员可能会创建或更新用户或其他对象。
> 任何一个角色都不授权管理“条件访问”设置。

## <a name="create-and-assign-a-custom-role-preview"></a>创建和分配自定义角色（预览版）

创建自定义角色和分配自定义角色是不同的步骤：

- [创建自定义角色定义 *，并*通过预设列表为该角色添加权限](roles-create-custom.md)。[](roles-custom-available-permissions.md) 这些权限与内置角色中使用的权限相同。
- [创建角色分配以分配自定义角色 *。* ](roles-assign-powershell.md)

借助这种隔离可以创建单个角色定义，然后在不同的范围多次分配该角色。 可以在组织范围分配自定义角色，或者在单个 Azure AD 对象范围分配自定义角色。 例如，单个应用注册就属于对象范围。 使用不同的范围可为 Sally 分配对组织中所有应用注册的相同角色定义，并只为 Naveen 分配对 Contoso Expense Reports 应用注册的角色定义。

创建和使用用于委托应用程序管理权的自定义角色时的提示：
- 自定义角色只会在 Azure AD 门户的“最近的应用注册”边栏选项卡授予访问权限。 他们不会在“旧应用注册”边栏选项卡中授予访问权限。
- 当“限制访问 Azure AD 管理门户”用户设置指定为“是”时，自定义角色不会授予对 Azure AD 门户的访问权限。
- 用户有权使用角色分配访问的应用注册只会显示在“应用注册”页上的“所有应用程序”选项卡中。 它们不会显示在“拥有的应用程序”选项卡中。

若要详细了解自定义角色的基础知识，请参阅[自定义角色概述](roles-custom-overview.md)，以及[创建自定义角色](roles-create-custom.md)和[分配角色](roles-assign-powershell.md)。

## <a name="next-steps"></a>后续步骤

- [应用程序注册子类型和权限](roles-custom-available-permissions.md)
- [Azure AD 管理员角色参考](directory-assign-admin-roles.md)
