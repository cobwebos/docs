---
title: 新的 Azure 门户应用注册体验
titleSuffix: Microsoft identity platform
description: Azure 门户中的新应用注册体验简介
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154587"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>新的 Azure 门户应用注册体验

Azure 门户中的新[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验有许多改进。 如果您更熟悉注册或管理融合应用程序（称为旧体验）的应用程序注册门户 （apps.dev.microsoft.com） 体验，本培训指南将让您开始使用新体验。

## <a name="whats-not-changing"></a>什么没有改变？

- 您的应用程序和相关配置可以在新体验中找到。 您无需再次注册应用程序，应用程序的用户也不需要再次登录。

    > [!NOTE]
    > 您必须使用用于注册应用程序以在 Azure 门户中查找应用程序的帐户登录。 我们建议您通过比较配置文件中的电子邮件地址，在 Azure 门户中检查登录用户与登录到应用程序注册门户的用户匹配。
    >
    > 在某些情况下，特别是当您使用带有 Azure AD 电子邮件地址的个人 Microsoft 帐户（例如 Outlook、Live、Xbox 等）登录时，我们发现，当您从旧体验转到 Azure 门户时，它会用相同的标记您进入其他帐户电子邮件在 Azure AD 租户中。 如果您仍然认为您的应用程序丢失，请注销并使用正确的帐户登录。

- Azure 门户中尚不支持使用个人 Microsoft 帐户创建的实时 SDK 应用，在不久的将来将继续保留旧体验。

## <a name="key-changes"></a>关键更改

-   在旧体验中，应用默认注册为支持所有组织帐户（多租户）和个人 Microsoft 帐户的聚合应用。 无法通过旧体验修改此功能，因此很难创建仅支持组织帐户的应用（多租户或单个租户）。
    新的体验允许您注册支持所有这些选项的应用。 [了解有关应用类型 的更多](active-directory-v2-registration-portal.md)信息。

-   在新的体验中，如果个人 Microsoft 帐户也位于 Azure AD 租户中，您将看到三个选项卡- 租户中的所有应用程序、租户中拥有的应用程序以及个人帐户中的应用程序。 因此，如果您认为在个人 Microsoft 帐户注册的应用丢失，请查看**来自个人帐户的应用程序**选项卡。

-   在新的体验中，您可以通过导航到配置文件并选择交换机目录，轻松地在租户之间切换。

## <a name="list-of-applications"></a>应用程序列表

-   新应用列表显示通过 Azure 门户中的旧应用注册体验（仅登录 Azure AD 帐户的应用）以及通过[应用程序注册门户](https://apps.dev.microsoft.com/)（同时登录 Azure AD 和个人 Microsoft 帐户的应用）注册的应用程序。

-   新应用列表有两个附加列：**在列上创建**，**证书&机密**列，显示已在应用上注册的凭据的状态（当前、即将过期或过期）。

## <a name="new-app-registration"></a>新应用注册

在旧体验中，要注册融合应用，您只需要提供名称。 创建的应用注册为支持所有组织目录（多租户）以及个人 Microsoft 帐户的聚合应用。  无法通过旧体验修改此功能，因此很难创建仅支持组织帐户的应用（多租户或单个租户）。 [了解有关受支持帐户类型详细信息](v2-supported-account-types.md)

在新体验中，必须提供应用的名称，并选择支持的帐户类型。 您可以选择提供重定向 URI。
如果提供重定向 URI，则需要指定它是 Web/公共的（本机/移动和桌面）。 有关如何使用新应用注册体验注册应用的详细信息，请参阅[此快速入门](quickstart-register-app.md)。

## <a name="app-management-page"></a>应用管理页面

旧体验具有用于融合应用的单个应用管理页，该页面包含以下部分：属性、应用程序机密、平台、所有者、Microsoft 图形权限、配置文件和高级选项。

Azure 门户中的新体验将这些功能表示为单独的页面。 您可以在其中找到等效功能：

-   属性 - 名称和应用程序 ID 位于"概述"页上。

-   应用程序机密位于证书&机密页上

-   平台配置位于身份验证页上

-   Microsoft 图形权限与其他权限一起位于 API 权限页上

-   个人资料位于品牌页面上

-   高级选项 - 实时 SDK 支持位于身份验证页面上。

## <a name="application-secretscertificates--secrets"></a>应用程序机密/证书&机密

在新的体验中，**应用程序机密**已重命名为**证书&机密**。 此外，“公钥”现在称为“证书”，“密码”称为“客户端机密”。**************** 出于安全原因，我们选择不将此功能引入新体验中，因此，您无法再生成新的密钥对。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平台/身份验证：回复 URL/重定向 URI
在旧体验中，应用具有 Web、本机和 Web API 的平台部分，用于配置重定向 URL、注销 URL 和隐式流。

在新体验中，可以在应用\'的身份验证部分找到回复 URL。 此外，它们称为重定向 URI，重定向 URI 的格式已更改。 它们需要与应用类型（Web 或公共客户端 - 移动客户端和桌面）相关联。 [了解详细信息](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web API 在"公开 API"页中配置。

> [!NOTE]
> 尝试新的“身份验证设置”体验，在其中可以根据要面向的平台或设备配置应用程序的设置。 [了解详细信息](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>微软图形权限/API 权限

-   在旧体验中选择 API 时，只能从 Microsoft 图形 API 中进行选择。 在新的体验中，您可以从许多 Microsoft API 中进行选择，包括 Microsoft 图形、组织的 API 和 API，这三个选项卡显示：Microsoft API、我的组织使用的 API 或我的 API。 在“我的组织使用的 API”选项卡上的搜索栏可以搜索租户中的服务主体。

    > [!NOTE]
    > 如果应用程序未与租户相关联，则不会显示此选项卡。 有关如何使用新体验请求权限的详细信息，请参阅[此快速入门](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)。

-   旧体验没有 **"授予权限**"按钮。 新体验提供“授予许可”部分，并在应用的“API 权限”部分提供“授予管理员许可”按钮。**** 只有管理员可以授予同意，并且此按钮仅为管理员启用。 当管理员选择 **"授予管理员同意"** 按钮时，将授予所有请求的权限管理员同意。

## <a name="profile"></a>配置文件
在旧体验中，配置文件具有徽标、主页 URL、服务条款 URL 和隐私声明 URL 配置。 在新的体验中，可以在"品牌"页面找到这些。

## <a name="application-manifest"></a>应用程序清单
在新体验中，清单页面允许您编辑和更新应用的属性。 有关详细信息，请参阅[应用程序清单](reference-app-manifest.md)。

## <a name="new-ui"></a>新 UI
对于以前只能使用清单编辑器或 API 设置的或者不存在的属性，现在提供了新的 UI。

-   在“身份验证”页上可以找到“隐式授权流”(oauth2AllowImplicitFlow)。 与旧体验不同，您可以启用访问令牌或 ID 令牌，或同时启用这两种令牌。

-   可以通过“公开 API”页配置“此 API 定义的范围”(oauth2Permissions) 和“已授权的客户端应用程序”(preAuthorizedApplications)。 有关如何将应用配置为 Web API 和公开权限/范围的详细信息，请参阅[此快速入门](quickstart-configure-app-expose-web-apis.md)。

-   发布商域（在[\'应用程序同意提示](application-consent-experience.md)符上向用户显示）可以在"品牌"边栏选项卡页面上找到。 有关如何配置发布者域的详细信息，请参阅[此操作指南](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新体验具有以下限制：

-   新体验尚不支持 Azure AD B2C 租户的应用注册。

-   新体验尚不支持使用个人 Microsoft 帐户创建的实时 SDK 应用。

-   不支持在 UI 中更改受支持帐户的值。 您需要使用应用清单，除非您\'在 Azure AD 单租户和多租户之间切换。

   > [!NOTE]
   > 如果您是 Azure AD 租户中的个人 Microsoft 帐户用户，并且租户管理员对 Azure 门户的访问受到限制，则可能会拒绝访问。 但是，如果您在搜索栏中键入应用注册或固定该快捷方式来访问新体验。
