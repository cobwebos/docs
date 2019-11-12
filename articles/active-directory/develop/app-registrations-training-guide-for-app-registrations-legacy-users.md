---
title: 用于从应用注册（旧）过渡到 Azure 门户新应用注册体验的培训指南
description: Azure 门户中的新应用注册体验简介
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01d543262a9eb358643c0860b24ac4306d2c5edf
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927096"
---
# <a name="transitioning-from-app-registrations-legacy-to-the-new-app-registrations-experience-in-the-azure-portal"></a>从应用注册（旧）转换到 Azure 门户的新应用注册体验

您可以在 Azure 门户中找到许多新[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验的改进。 如果你熟悉 Azure 门户中的应用注册（旧）体验，请使用本培训指南开始使用新体验。

在 Azure Active Directory 中，此处所述的新应用程序注册体验已公开发布（GA）。 在 Azure Active Directory B2C （Azure AD B2C）中，此体验处于预览阶段。

## <a name="key-changes"></a>关键更改

- 应用注册不局限于 *Web 应用/API* 或*本机*应用。 你可以通过注册各自的重定向 Uri，对所有这些应用使用相同的应用注册。

- 旧体验支持使用组织（Azure AD）帐户登录的应用。 应用已注册为单租户。 仅支持应用在其中注册了应用的目录的组织帐户。 可将应用修改为多租户，并支持所有组织帐户。 新体验允许注册可同时支持这两个选项和第三个选项的应用：所有组织帐户和个人 Microsoft 帐户。

- 仅当已使用组织帐户登录到 Azure 门户后，才可以使用旧体验。 使用新体验，你可以使用不与目录关联的个人 Microsoft 帐户。

## <a name="list-of-applications"></a>应用程序列表

新应用列表显示通过 Azure 门户中的旧版应用注册体验注册的应用程序。 这些应用使用 Azure AD 帐户登录。 新应用列表还显示通过应用程序注册门户注册的应用。 这些应用使用 Azure AD 和个人 Microsoft 帐户登录。

>[!NOTE]
>应用程序注册门户已弃用。

新应用列表没有**应用程序类型**列，因为单个应用程序注册可以是多个类型。 此列表有两个附加列： **"创建于" 和 "证书" & "** **机密**"。 **证书 & 机密**显示已在应用上注册的凭据的状态。 状态包括**当前**、**即将到期**和已**过期**。

## <a name="new-app-registration"></a>新应用注册

在旧体验中，若要注册应用，需要提供：**名称**、**应用程序类型**和**登录 URL/重定向 URI**。 创建的应用仅 Azure AD 单租户应用程序。 它们仅支持来自注册了应用的目录的组织帐户。

在新体验中，必须提供应用的**名称**，并选择**支持的帐户类型**。 你可以选择提供**重定向 URI**。 如果提供重定向 URI，则需要指定它是否为 web/公共（移动和桌面）。 有关详细信息，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。 有关 Azure AD B2C，请参阅[在 Azure Active Directory B2C 中注册应用程序](../../active-directory-b2c/tutorial-register-applications.md)。

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>应用程序注册门户与应用注册页面之间的差异

### <a name="the-legacy-properties-page"></a>旧“属性”页

旧体验具有 "**属性**" 页。 **属性**具有以下字段：

- **名称**
- **对象 ID**
- 应用程序 ID
- 应用 ID URI
- **徽标**
- **主页 URL**
- **注销 URL**
- **服务条款 URL**
- **隐私声明 URL**
- **应用程序类型**
- **多租户**

新体验没有此页。 下面是可以找到等效功能的位置：

- **名称**、**徽标**、**主页 url**、**服务条款 url**和**隐私声明 url**现在位于应用的**品牌**页面上。
- "**概述**" 页上有**对象 Id**和**应用程序（客户端） id** 。
- 旧体验中由“多租户”切换开关控制的功能已由“身份验证”页上的“支持的帐户类型”取代。 有关详细信息，请参阅[快速入门：修改应用程序支持的帐户](quickstart-modify-supported-accounts.md)。
- “注销 URL”现在位于“身份验证”页上。
- “应用程序类型”不再是有效字段。 相反，可以在 "**身份验证**" 页上找到的重定向 uri，确定支持哪些应用类型。
- **应用 ID uri**现在称为**应用程序 id uri** ，你可以在**公开 API**时找到它。 在旧体验中，此属性是使用以下格式 autoregistered 的： `https://{tenantdomain}/{appID}`，例如 `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`。 在新体验中，它作为 `api://{appID}`自动生成，但它需要显式保存。 在 Azure AD B2C 租户中，仍使用 `https://{tenantdomain}/{appID}` 格式。

### <a name="reply-urlsredirect-urls"></a>回复 URL/重定向 URI

在旧体验中，应用包含“回复 URL”页。 在新体验中，可以在应用的 "**身份验证**" 页上找到 "回复 url"。 它们现在称为**重定向 uri**。

重定向 Uri 的格式已更改。 它们需要与应用类型（web 或公共）相关联。 出于安全原因，不支持通配符和 `http://` 方案（ *http://localhost* 除外）。

### <a name="keyscertificates--secrets"></a>密钥/证书和机密

在旧体验中，应用包含“密钥”页。 在新体验中，该设置已重命名为“证书和机密”。

**公钥**现在称为**证书**。 **密码**现在称为**客户端**密码。

### <a name="required-permissionsapi-permissions"></a>所需的权限/API 权限

在旧体验中，应用包含“所需的权限”页。 在新体验中，该设置已重命名为“API 权限”。

当你在旧版体验中选择 API 时，你可以从 Microsoft Api 的小型列表中进行选择。 你还可以在租户中搜索服务主体。 在新体验中，可以从多个选项卡中进行选择： **Microsoft api**、**我组织使用的 api**或**api**。 在“我的组织使用的 API”选项卡上的搜索栏可以搜索租户中的服务主体。

> [!NOTE]
> 如果应用程序未与租户相关联，则不会显示此选项卡。 有关如何请求权限的详细信息，请参阅[快速入门：配置客户端应用程序以访问 Web api](quickstart-configure-app-access-web-apis.md)。

旧体验在“请求的权限”页面顶部提供了“授予权限”按钮。 在新体验中，"**授予许可**" 页在应用的**API 权限**部分中有 "**授予管理员" 许可**按钮。 按钮的工作方式也存在一些差异。

在旧体验中，逻辑根据已登录用户和请求的权限而异。 逻辑是：

- 如果只请求了用户同意的权限，并且登录用户不是管理员，则该用户可以向用户授予请求的权限。
- 如果至少有一个要求管理员同意的权限，并且登录用户不是管理员，则在尝试授予许可时，用户将收到错误消息。
- 如果登录的用户是管理员，则针对所有请求的权限授予管理员许可。

在新体验中，只有管理员能够授予许可。 当管理员选择 "**授予管理员许可**" 时，管理员同意将授予所有请求的权限。

## <a name="deleting-an-app-registration"></a>删除应用注册

在旧体验中，你只能删除单租户应用。 对于多租户应用，已禁用删除按钮。 在新体验中，你可以删除处于任何状态的应用，但必须确认该操作。 有关详细信息，请参阅[快速入门：删除注册到 Microsoft 标识平台的应用程序](quickstart-remove-app.md)。

## <a name="application-manifest"></a>应用程序清单

旧体验和新体验对清单编辑器中的 JSON 格式使用不同的版本。 有关详细信息，请参阅[Azure Active Directory 应用程序清单](reference-app-manifest.md)。

## <a name="new-ui"></a>新 UI

新体验为以下属性添加了 UI 控件：

- "**身份验证**" 页具有**隐式授权流**（`oauth2AllowImplicitFlow`）。 与旧版体验不同，你可以启用**访问令牌**或**ID 令牌**，或者同时启用两者。
- "**公开 api** " 页包含由此 API （`oauth2Permissions`）和**授权客户端应用程序**（`preAuthorizedApplications`）**定义的作用域**。 有关如何将应用配置为 web API 并公开权限/范围的详细信息，请参阅[快速入门：将应用程序配置为公开 Web api](quickstart-configure-app-expose-web-apis.md)。
- **品牌**页面包含**发布者域**。 在[应用程序的许可提示下](application-consent-experience.md)向用户显示发布者域。 有关详细信息，请参阅[如何：配置应用程序的发布服务器域](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新体验具有以下限制：

- 客户端密码的格式（应用密码）不同于旧体验的格式，可能会破坏 CLI。
- 不支持在 UI 中更改受支持帐户的值。 除非在 Azure AD 单租户与多租户之间切换，否则需要使用应用清单。
