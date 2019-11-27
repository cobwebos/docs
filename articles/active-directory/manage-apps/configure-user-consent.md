---
title: 配置用户同意应用程序-Azure Active Directory
description: 了解如何管理用户许可应用程序权限的方式。 可以通过授予管理员许可来来简化用户体验。 这些方法适用于 Azure Active Directory (Azure AD) 租户中的所有最终用户。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273985"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>配置最终用户对 Azure Active Directory 中应用程序的许可方式
了解如何配置用户许可应用程序权限的方式。 可以通过授予管理员许可来来简化用户体验。 本文提供配置用户许可的不同方式。 这些方法适用于 Azure Active Directory (Azure AD) 租户中的所有最终用户。 

有关许可应用程序的详细信息，请参阅 [Azure Active Directory 许可框架](../develop/consent-framework.md)。

## <a name="prerequisites"></a>先决条件

授予管理员许可需要以全局管理员、应用程序管理员或云应用程序管理员的身份登录。

若要限制对应用程序的访问，你需要要求用户分配，然后将用户或组分配到应用程序。  有关详细信息，请参阅[分配用户和组的方法](methods-for-assigning-users-and-groups.md)。

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>在 Azure 门户中授予对企业应用的管理员许可

授予对企业应用的管理员许可：

1. 以全局管理员、应用程序管理员或云应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航菜单的顶部单击“所有服务”。 此时会打开“Azure Active Directory 扩展”。
3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。
4. 在导航菜单中，单击“企业应用程序”。
5. 选择要同意的应用程序。
6. 选择 "**权限**"，然后单击 "**授予管理员许可**"。 系统将提示你登录以管理应用程序。
7. 使用有权授予对应用程序的管理员许可的帐户登录。 
8. 许可应用程序权限。

仅当应用程序符合以下条件时，此选项才起作用： 

- 已在租户中注册，或
- 已在另一个 Azure AD 租户中注册，并且已由至少一个最终用户许可。 最终用户许可应用程序后，Azure AD 将在 Azure 门户中的“企业应用”下列出该应用程序。

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>在 Azure 门户中注册应用时授予管理员许可

在注册应用时授予管理员许可： 

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“应用注册”边栏选项卡。
3. 选择要许可的应用程序。
4. 选择“API 权限”。
5. 单击 "**授予管理员许可**"。


## <a name="grant-admin-consent-through-a-url-request"></a>通过 URL 请求授予管理员许可

通过 URL 请求授予管理员许可：

1. 使用应用程序配置向 *login.microsoftonline.com* 构造请求，并将其追加到 `&prompt=admin_consent` 中。 此 URL 将如下所示： `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. 使用管理员凭据登录后，该应用即已授予所有用户的同意。


## <a name="force-user-consent-through-a-url-request"></a>通过 URL 请求强制用户许可

若要强制要求最终用户在每次身份验证时都要许可应用程序，请将 `&prompt=consent` 追加到身份验证请求 URL。
此 URL 将如下所示： `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>后续步骤

[同意并将应用集成到 AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[同意并为 AzureAD v2.0 聚合应用授予权限](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
