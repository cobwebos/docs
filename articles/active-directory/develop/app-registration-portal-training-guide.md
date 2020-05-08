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
ms.openlocfilehash: 3a954167dc0698389680a92511621fb6acf4b12b
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889997"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>新的 Azure 门户应用注册体验

Azure 门户的新[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验有很多改进。 如果你对注册或管理应用程序的应用程序注册门户（apps.dev.microsoft.com）更熟悉，这里称为 "旧经验"），本指南将帮助你开始使用新的体验。

## <a name="whats-not-changing"></a>什么不更改？

- 你的应用程序和相关配置可以在新体验中按原样找到。 无需再次注册应用程序，应用程序的用户将不需要再次登录。

    > [!NOTE]
    > 必须使用注册应用程序时使用的帐户登录，以便在 Azure 门户中查找它们。 建议你查看 Azure 门户中已登录的用户是否与已登录到应用程序注册门户的用户相匹配，方法是将你的个人资料中的电子邮件地址进行比较。
    >
    > 在某些情况下，特别是当你使用 Azure AD 电子邮件地址登录时，尤其是在使用个人 Microsoft 帐户（例如 Outlook、Live、Xbox 等）进行登录的情况下，我们发现，当你从旧经验中访问该 Azure 门户时，它会在你的 Azure AD 租户中使用同一电子邮件登录到不同的帐户。 如果你仍相信你的应用程序丢失，请注销并以正确的帐户登录。

- 在 Azure 门户中尚不支持使用个人 Microsoft 帐户创建的 Live SDK 应用，并将在不久的将来继续保留旧体验。

## <a name="key-changes"></a>关键更改

-   在过去的体验中，应用默认注册为*汇聚*应用-支持所有组织帐户（多租户）和个人 Microsoft 帐户的应用。 这不能通过旧体验进行修改，因此很难创建仅支持组织帐户（多租户或单租户）的应用。
    新体验允许注册支持所有这些选项的应用。 [了解有关应用类型的详细信息](active-directory-v2-registration-portal.md)。

-   在新体验中，如果你的个人 Microsoft 帐户也在 Azure AD 租户中，你将看到三个选项卡：租户中的所有应用程序、租户中的所有应用程序以及你的个人帐户中的应用程序。 因此，如果您认为注册到个人 Microsoft 帐户的应用程序丢失，请从 "**个人帐户**" 选项卡中检查应用程序。

-   在新体验中，你可以通过导航到你的配置文件并选择 "切换目录" 来轻松地在租户之间切换。

## <a name="list-of-applications"></a>应用程序列表

-   新的应用列表显示了通过 "Azure 门户中的旧版应用注册体验注册的应用程序（仅限登录 Azure AD 帐户的应用）以及通过[应用程序注册门户](https://apps.dev.microsoft.com/)注册的应用程序（同时登录 Azure AD 和个人 Microsoft 帐户）。

-   新应用列表有两个附加列： **"基于**列和**证书**创建" & "机密" 列，显示已在应用上注册的凭据的状态（当前、即将到期或已过期）。

## <a name="new-app-registration"></a>新应用注册

在过去的体验中，若要注册应用，只需提供一个名称。 已创建的应用注册为*汇聚*应用-支持所有组织目录（多租户）和个人 Microsoft 帐户的应用。  这种情况无法通过旧体验进行修改，因此很难创建仅支持组织帐户（单租户或多租户）的应用。 [了解有关支持的帐户类型的详细信息](v2-supported-account-types.md)

在新体验中，必须提供应用的名称，并选择支持的帐户类型。 你可以选择提供重定向 URI。
如果提供重定向 URI，则需要指定其 web/公共（本机/移动和桌面）。 有关如何使用新应用注册体验注册应用的详细信息，请参阅[此快速入门](quickstart-register-app.md)。

## <a name="app-management-page"></a>应用管理页

对于具有以下各部分的应用，旧体验具有单个应用管理页：属性、应用程序机密、平台、所有者、Microsoft Graph 权限、配置文件和高级选项。

Azure 门户中的新体验在单独的页面中提供这些功能。 下面是可以找到等效功能的位置：

- 属性-名称和应用程序 ID 在 "概述" 页上。
- 应用程序机密位于 "证书 & 机密" 页面上
- 平台配置位于 "身份验证" 页上
- Microsoft Graph 权限位于 "API 权限" 页上以及其他权限
- 配置文件处于品牌页面
- 高级选项-实时 SDK 支持在 "身份验证" 页上。

## <a name="application-secretscertificates--secrets"></a>应用程序机密/证书 & 机密

在新体验中，**应用程序密码**已重命名为**证书 & 密码**。 此外，“公钥”现在称为“证书”，“密码”称为“客户端机密”。**************** 出于安全原因，我们选择不引入此功能，因此，你不能再生成新的密钥对。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平台/身份验证：回复 Url/重定向 Uri
在过去的体验中，应用具有适用于 Web、本机和 Web API 的平台部分，用于配置重定向 Url、注销 URL 和隐式流。

在新体验中，可以在 "应用程序\'身份验证" 部分找到 "回复 url"。 此外，它们也称为重定向 Uri，重定向 Uri 的格式已更改。 它们需要与应用类型（web 或公用客户端-移动和桌面）相关联。 [了解详细信息](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Web Api 在公开 API 页中进行配置。

> [!NOTE]
> 尝试新的“身份验证设置”体验，在其中可以根据要面向的平台或设备配置应用程序的设置。  [了解详细信息](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph 权限/API 权限

-   在旧体验中选择 API 时，只能从 Microsoft Graph Api 中进行选择。 在新体验中，你可以从多个 Microsoft Api （Microsoft Graph 包括来自你的组织的 Api 和你的 Api）中进行选择，这将显示在三个选项卡中： Microsoft Api、我的组织使用的 Api 或 Api。 在“我的组织使用的 API”选项卡上的搜索栏可以搜索租户中的服务主体。

    > [!NOTE]
    > 如果应用程序未与租户相关联，则不会显示此选项卡。 有关如何使用新体验请求权限的详细信息，请参阅[此快速入门](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)。

-   旧体验没有 "**授予权限**" 按钮。 新体验提供“授予许可”部分，并在应用的“API 权限”部分提供“授予管理员许可”按钮。**** 只有管理员才能授予同意，此按钮仅为管理员启用。 当管理员选择 "**授予管理员许可**" 按钮时，管理员同意将授予所有请求的权限。

## <a name="profile"></a>配置文件
在旧体验中，配置文件具有徽标、主页 URL、服务条款 url 和隐私声明 URL 配置。 在新体验中，可以在 "品牌" 页中找到。

## <a name="application-manifest"></a>应用程序清单
在新的体验中，可以通过 "清单" 页编辑和更新应用的属性。 有关详细信息，请参阅[应用程序清单](reference-app-manifest.md)。

## <a name="new-ui"></a>新 UI
对于以前只能使用清单编辑器或 API 设置的或者不存在的属性，现在提供了新的 UI。

-   在“身份验证”页上可以找到“隐式授权流”(oauth2AllowImplicitFlow)。 与旧经验不同，你可以启用访问令牌或 ID 令牌，或者同时启用两者。

-   可以通过“公开 API”页配置“此 API 定义的范围”(oauth2Permissions) 和“已授权的客户端应用程序”(preAuthorizedApplications)。 有关如何将应用配置为 Web API 和公开权限/范围的详细信息，请参阅[此快速入门](quickstart-configure-app-expose-web-apis.md)。

-   发布者域（在[应用程序\'许可提示](application-consent-experience.md)中向用户显示）可以在 "品牌" 页中找到。 有关如何配置发布者域的详细信息，请参阅[此操作指南](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新体验具有以下限制：

-   新体验尚不支持 Azure AD B2C 租户应用注册。

-   新体验尚不支持用个人 Microsoft 帐户创建的 Live SDK 应用。

-   不支持在 UI 中更改受支持帐户的值。 你需要使用应用程序清单，除非你\'在 Azure AD 单租户和多租户之间进行切换。

   > [!NOTE]
   > 如果你是 Azure AD 租户中的个人 Microsoft 帐户用户，并且租户管理员限制了对 Azure 门户的访问权限，则你可能会收到访问被拒绝的情况。 但是，如果通过在搜索栏中键入应用注册来浏览快捷方式，则可以访问新的体验。

## <a name="next-steps"></a>后续步骤

若要开始使用新的应用注册体验，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。
