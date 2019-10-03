---
title: Azure 经典订阅管理员 | Microsoft Docs
description: 介绍如何添加或更改 Azure 共同管理员和服务管理员角色，以及如何查看帐户管理员。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/01/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9c5e87d8d6fe49302bee2b2248f84ba98a650533
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802314"
---
# <a name="azure-classic-subscription-administrators"></a>Azure 经典订阅管理员

Microsoft 建议使用基于角色的访问控制 (RBAC) 管理对 Azure 资源的访问权限。 但是，如果你仍在使用经典部署模型，则需要使用经典订阅管理员角色：服务管理员和共同管理员。 有关详细信息，请参阅 [Azure 资源管理器与经典部署](../azure-resource-manager/resource-manager-deployment-model.md)。

本文介绍如何添加或更改共同管理员和服务管理员角色，以及如何查看帐户管理员。

## <a name="add-a-co-administrator"></a>添加共同管理员

> [!TIP]
> 如果用户需要使用 [Azure 服务管理 PowerShell 模块](https://docs.microsoft.com/powershell/module/servicemanagement/azure)管理 Azure 经典部署，则你只需添加共同管理员。 如果用户只使用 Azure 门户管理经典资源，则无需为该用户添加经典管理员。

1. 以服务管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

    只能在订阅范围分配共同管理员。

1. 单击“访问控制(IAM)”。

1. 单击“经典管理员”选项卡。

    ![打开经典管理员的屏幕截图](./media/classic-administrators/classic-administrators.png)

1. 单击“添加” > “添加共同管理员”打开“添加共同管理员”窗格。

    如果“添加共同管理员”选项已禁用，则表示你没有相应的权限。

1. 选择要添加的用户，然后单击“添加”。

    ![添加协同管理员的屏幕截图](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>将来宾用户添加为共同管理员

已分配共同管理员角色的[来宾用户](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md)与具有共同管理员角色的成员用户之间可能有一些区别。 请考虑以下方案：

- 具有 Azure AD 帐户（工作或学校帐户）的用户 A 是 Azure 订阅的服务管理员。
- 用户 B 是 Microsoft 帐户。
- 用户 A 向用户 B 分配共同管理员角色。
- 用户 B 几乎可以执行所有操作，但无法注册应用程序或查找 Azure AD 目录中的用户。

你希望用户 B 可以管理所有内容。 此区别的原因在于，Microsoft 帐户作为来宾用户（而不是成员用户）添加到订阅。 与成员用户相比，来宾用户在 Azure AD 中具有不同的默认权限。 例如，成员用户可以在 Azure AD 中读取其他用户，而来宾用户不能。 成员用户可以在 Azure AD 中注册新的服务主体，而来宾用户不能。

如果来宾用户需要能够执行这些任务，则可能的解决方案是分配来宾用户所需的特定 Azure AD 管理员角色。 例如，在上一方案中，你可以分配[目录读取者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)角色以便读取其他用户，并分配[应用程序开发人员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer)角色以便能够创建服务主体。 有关成员和来宾用户及其权限的详细信息，请参阅 [Azure Active Directory 中的默认用户权限是什么？](../active-directory/fundamentals/users-default-permissions.md)。

请注意，[Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)与 [Azure AD 管理员角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md)不同。 内置角色不授予对 Azure AD 的任何访问权限。 有关详细信息，请参阅[了解不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="remove-a-co-administrator"></a>删除共同管理员

1. 以服务管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

1. 单击“访问控制(IAM)”。

1. 单击“经典管理员”选项卡。

1. 勾选要删除的共同管理员。

1. 单击“删除”。

1. 在出现的消息框中单击“是”。

    ![删除协同管理员的屏幕截图](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>更改服务管理员

只有帐户管理员可以更改订阅的服务管理员。 默认情况下，当你注册 Azure 订阅时，服务管理员即是帐户管理员。 具有帐户管理员角色的用户无权访问 Azure 门户。 具有服务管理员角色的用户拥有 Azure 门户的完全访问权限。 如果帐户管理员和服务管理员是同一个用户，而你将服务管理员更改为其他用户，则帐户管理员会失去 Azure 门户的访问权限。 但是，帐户管理员可始终使用帐户中心将服务管理员重新更改为自己。

可通过两种方式更改服务管理员。 可以在 **Azure 门户**或**帐户中心**内进行更改。

### <a name="azure-portal"></a>Azure 门户

1. 请查看服务管理员变更限制，确保自己的方案受支持。

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

1. 单击“属性”。

    ![显示帐户管理员的屏幕截图](./media/classic-administrators/account-admin.png)

1. 在顶部单击“服务管理员”打开“服务管理”窗格。

    如果“服务管理员”按钮已禁用，则表示你没有相应的权限。 只有充当帐户管理员的用户可以更改服务管理员。

1. 选择新的服务管理员，然后单击“保存”。

### <a name="account-center"></a>帐户中心

1. 请查看服务管理员变更限制，确保自己的方案受支持。

1. 以帐户管理员身份登录到[帐户中心](https://account.windowsazure.com/subscriptions)。

1. 单击某个订阅。

1. 在右侧单击“编辑订阅详细信息”。

    ![显示帐户中心中“编辑订阅”按钮的屏幕截图](./media/classic-administrators/editsub.png)

1. 在“服务管理员”框中，输入新服务管理员的电子邮件地址。

    ![屏幕截图：用于更改服务管理员电子邮件的框](./media/classic-administrators/change-service-admin.png)

1. 单击勾选标记以保存更改。

### <a name="limitations-for-changing-the-service-administrator"></a>服务管理员变更限制

每个 Azure 订阅只能有一个服务管理员。 更改服务管理员的行为会有所不同，具体取决于帐户管理员是 Microsoft 帐户还是 Azure AD 帐户（工作或学校帐户）。

| 帐户管理员帐户 | 是否可以将服务管理员更改为其他 Microsoft 帐户？ | 是否可以将服务管理员更改为同一目录中的 Azure AD 帐户？ | 是否可以将服务管理员更改为其他目录中的 Azure AD 帐户？ |
| --- | --- | --- | --- |
| Microsoft 帐户 | 是 | 否 | 否 |
| Azure AD 帐户 | 是 | 是 | 否 |

如果帐户管理员是 Azure AD 帐户，则可以将服务管理员更改为同一目录中的 Azure AD 帐户，但不能更改为其他目录。 例如，abby@contoso.com 会将服务管理员更改为 bob@contoso.com，但无法将服务管理员更改为 john@notcontoso.com，除非 john@notcontoso.com 在 contoso.com 目录中存在。

有关 Microsoft 帐户和 Azure AD 帐户的详细信息，请参阅[什么是 Azure Active Directory？](../active-directory/fundamentals/active-directory-whatis.md)。

## <a name="view-the-account-administrator"></a>查看帐户管理员

帐户管理员是最初注册 Azure 订阅的用户，充当订阅的账单所有者。 若要更改订阅的账户管理员，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)。

遵循以下步骤查看帐户管理员：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 打开[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)并选择一个订阅。

1. 单击“属性”。

    订阅的帐户管理员会显示在“帐户管理员”框中。

    ![显示帐户管理员的屏幕截图](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>后续步骤

* [了解 Azure 中的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [使用 RBAC 和 Azure 门户管理对 Azure 资源的访问权限](../role-based-access-control/role-assignments-portal.md)
* [添加或更改 Azure 订阅管理员](../billing/billing-add-change-azure-subscription-administrator.md)
