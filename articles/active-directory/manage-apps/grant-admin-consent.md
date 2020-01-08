---
title: 向租户范围内的管理员授予同意应用程序 Azure AD
description: 了解如何向应用程序授予租户范围的许可，以便在登录到应用程序时不会提示最终用户同意。
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480913"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>向应用程序授予租户范围的管理员许可

了解如何通过向应用程序授予租户范围的管理员许可来简化用户体验。 本文提供了不同的方法来实现此目的。 这些方法适用于 Azure Active Directory (Azure AD) 租户中的所有最终用户。

有关许可应用程序的详细信息，请参阅 [Azure Active Directory 许可框架](../develop/consent-framework.md)。

## <a name="prerequisites"></a>必备组件

如果授予租户范围的管理员许可，则需要以 "[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)"、"[应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)" 或 "[云应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)" 身份登录。

> [!IMPORTANT]
> 当应用程序被授予租户范围的管理员许可时，所有用户都将能够登录到应用程序，除非已将其配置为要求用户分配。 若要限制可登录到应用程序的用户，需要用户分配，然后将用户或组分配给应用程序。 有关详细信息，请参阅[分配用户和组的方法](methods-for-assigning-users-and-groups.md)。

> [!WARNING]
> 向应用程序授予租户范围内的管理员许可，会向应用程序和应用程序的发布者授予对组织数据的访问权限。 在授予同意之前，请仔细查看应用程序请求的权限。

## <a name="grant-admin-consent-from-the-azure-portal"></a>向管理员授予许可 Azure 门户

### <a name="grant-admin-consent-in-enterprise-apps"></a>授予管理员在企业应用程序中的许可

如果已在租户中设置了应用程序，则可以通过*企业应用程序*向租户范围内的管理员授予许可。 例如，如果至少有一个用户已同意该应用程序，则可以在租户中设置该应用程序。 有关详细信息，请参阅[将应用程序添加到 Azure Active Directory 的方式和原因](../develop/active-directory-how-applications-are-added.md)。

向租户范围内的管理员授予在**企业应用程序**中列出的应用的许可：

1. 以 "[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)"、"[应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)" 或 "[云应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)" 身份登录到[Azure 门户](https://portal.azure.com)。
2. 选择 " **Azure Active Directory** " "**企业应用程序**"。
3. 选择要向其授予租户范围管理员许可的应用程序。
4. 选择 "**权限**"，然后单击 "**授予管理员许可**"。
5. 仔细查看应用程序所需的权限。
6. 如果你同意应用程序所需的权限，请授予同意。 否则，单击 "**取消**" 或关闭该窗口。

### <a name="grant-admin-consent-in-app-registrations"></a>授予管理员许可应用注册

对于你的组织已开发的应用程序，或直接在 Azure AD 租户中注册的应用程序，你还可以向租户范围内的管理员授予 Azure 门户中的**应用注册**。

若要向租户范围内的管理员授予**应用注册**：

1. 以 "[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)"、"[应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)" 或 "[云应用程序管理员](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)" 身份登录到[Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**然后**应用注册**"。
3. 选择要向其授予租户范围管理员许可的应用程序。
4. 选择 " **API 权限**"，然后单击 "**授予管理员许可**"。
5. 仔细查看应用程序所需的权限。
6. 如果你同意应用程序所需的权限，请授予同意。 否则，单击 "**取消**" 或关闭该窗口。

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>构造用于授予租户范围管理员许可的 URL

使用上述任一方法授予租户范围内的管理员许可时，会从 Azure 门户打开一个窗口，提示进行租户范围的管理员许可。 如果知道应用程序的客户端 ID （也称为应用程序 ID），则可以生成相同的 URL 来授予租户范围内的管理员许可。

租户范围的管理员许可 URL 采用以下格式：

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

其中：

* `{client-id}` 是应用程序的客户端 ID （也称为应用 ID）。
* `{tenant-id}` 是你的组织的租户 ID 或任何已验证的域名。

与往常一样，在授予许可之前，请仔细查看应用程序请求的权限。

## <a name="next-steps"></a>后续步骤

[配置最终用户同意应用程序的方式](configure-user-consent.md)

[配置管理员许可工作流](configure-admin-consent-workflow.md)

[Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
