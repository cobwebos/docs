---
title: 新的 Azure 门户应用注册培训指南
description: 介绍新的 Azure 门户应用注册体验
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154570"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>新的 Azure 门户应用注册培训指南

在 Azure 门户上的新[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中，可以发现许多的改进。 如果你熟悉 Azure 门户中的应用注册（旧版）体验，可以参考本培训指南开始使用新的体验。

在 Azure Active Directory 中，本文所述的新应用程序注册体验已推出正式版 (GA)。 在 Azure Active Directory B2C (Azure AD B2C) 中，此体验以预览版提供。

## <a name="key-changes"></a>关键更改

- 应用注册不局限于 *Web 应用/API* 或*本机*应用。 注册相应的重定向 URI，即可对所有这些应用使用同一个应用注册。

- 旧体验仅支持使用组织 (Azure AD) 帐户登录的应用。 应用注册为单租户。 应用仅支持应用已注册到的目录中的组织帐户。 可将应用修改为多租户，然后它们可以支持所有组织帐户。 新的体验允许您注册可以同时支持这些选项以及第三个选项的应用：所有组织帐户以及个人 Microsoft 帐户。

- 仅当已使用组织帐户登录到 Azure 门户后，才可以使用旧体验。 借助新体验，您可以使用与目录无关的个人 Microsoft 帐户。

## <a name="list-of-applications"></a>应用程序列表

新应用列表显示通过 Azure 门户中旧应用注册体验注册的应用程序。 这些应用使用 Azure AD 帐户登录。 新的应用列表还显示通过应用程序注册门户注册的应用。 这些应用使用 Azure AD 和个人 Microsoft 帐户登录。

>[!NOTE]
>已弃用应用程序注册门户。

新应用列表不包含“应用程序类型”列，因为单个应用注册可能属于多种类型。**** 该列表有两个附加列：**在 上创建**和**证书&机密**。 “证书和机密”显示已在应用中注册的凭据的状态。**** 状态包括“当前”、“即将过期”和“已过期”。************

## <a name="new-app-registration"></a>新应用注册

在旧体验中，要注册需要提供的应用：**名称**、**应用程序类型**和**登录 URL/重定向 URI**。 创建的应用是仅限 Azure AD 的单租户应用程序。 它们仅支持应用已注册到的目录中的组织帐户。

在新体验中，必须提供应用的**名称**，并选择**支持的帐户类型**。 可以选择性地提供**重定向 URI**。 如果提供重定向 URI，需要指定它是否为 Web/公共 URI（移动和桌面）。 有关详细信息，请参阅[快速入门：使用 Microsoft 标识平台注册应用程序](quickstart-register-app.md)。 对于 Azure AD B2C，请参阅[在 Azure Active Directory B2C 中注册应用程序](../../active-directory-b2c/tutorial-register-applications.md)。

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>应用程序注册门户和应用注册页面之间的差异

### <a name="the-legacy-properties-page"></a>旧“属性”页

旧体验具有 **"属性"** 页。 **属性**具有以下字段：

- **名称**
- **对象 ID**
- **应用程序 ID**
- 应用 ID URI****
- **标志**
- **主页 URL**
- **注销 URL**
- **服务条款 URL**
- **隐私声明 URL**
- **应用程序类型**
- **多租户**

新体验没有该页面。 您可以在其中找到等效功能：

- **名称**，**徽标**，**主页 URL**，**服务条款 URL**和**隐私声明 URL**现在位于应用的品牌页面上。 **Branding**
- **对象 ID**和**应用程序（客户端）ID**位于 **"概述"** 页上。
- 旧体验中由“多租户”切换开关控制的功能已由“身份验证”页上的“支持的帐户类型”取代。************ 有关详细信息，请参阅[快速入门：修改应用程序支持的帐户](quickstart-modify-supported-accounts.md)。
- “注销 URL”现在位于“身份验证”页上。********
- “应用程序类型”不再是有效字段。**** 相反，重定向可在 **"身份验证"** 页上找到的 URI 确定支持哪些应用类型。
- **应用程序 ID URI**现在称为**应用程序 ID URI，** 您可以在**公开 API**上找到它。 在旧体验中，此属性使用以下格式自动注册： `https://{tenantdomain}/{appID}`， 例如 ， `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. 在新的体验中，它自动生成为`api://{appID}`，但需要显式保存。 在 Azure AD B2C`https://{tenantdomain}/{appID}`租户中，仍使用该格式。

### <a name="reply-urlsredirect-urls"></a>回复 URL/重定向 URI

在旧体验中，应用包含“回复 URL”页。**** 在新体验中，可以在应用的**身份验证**页面上找到回复 URL。 它们现在称为**重定向 URI。**

重定向 URI 的格式已更改。 它们需要与应用类型（Web 或公共）相关联。 出于安全原因，不支持通配`http://`符和方案，除非*http://localhost*.

### <a name="keyscertificates--secrets"></a>密钥/证书和机密

在旧体验中，应用包含“密钥”页。**** 在新体验中，该设置已重命名为“证书和机密”。****

**公钥**现在称为**证书**。 **密码**现在称为**客户端机密**。

### <a name="required-permissionsapi-permissions"></a>所需的权限/API 权限

在旧体验中，应用包含“所需的权限”页。**** 在新体验中，该设置已重命名为“API 权限”。****

在传统体验中选择 API 时，可以从 Microsoft API 的小列表中选择。 您还可以搜索租户中的服务主体。 在新的体验中，您可以从多个选项卡中进行选择：**微软 API、****我的组织使用的 API**或**我的 API**。 在“我的组织使用的 API”选项卡上的搜索栏可以搜索租户中的服务主体。****

> [!NOTE]
> 如果应用程序未与租户相关联，则不会显示此选项卡。 有关如何请求权限的详细信息，请参阅[快速入门：配置客户端应用程序以访问 Web API](quickstart-configure-app-access-web-apis.md)。

旧体验在“请求的权限”页面顶部提供了“授予权限”按钮。******** 在新体验中，"**授予同意"** 页在应用的**API 权限**部分上包含 **"授予管理员同意**"按钮。 按钮的功能方式也存在一些差异。

在旧体验中，逻辑根据已登录用户和请求的权限而异。 逻辑是：

- 如果仅请求用户同意权限，并且登录用户不是管理员，则用户可以授予用户对请求的权限的同意。
- 如果请求了至少一个需要管理员同意的权限，并且登录用户不是管理员，则用户在尝试授予同意时出错。
- 如果登录的用户是管理员，则针对所有请求的权限授予管理员许可。

在新体验中，只有管理员能够授予许可。 当管理员选择**授予管理员同意**时，将授予所有请求的权限管理员同意。

## <a name="deleting-an-app-registration"></a>删除应用注册

在旧体验中，只能删除单租户应用。 对于多租户应用，已禁用删除按钮。 在新体验中，可以删除处于任何状态的应用，但必须确认该操作。 有关详细信息，请参阅[快速入门：删除在 Microsoft 标识平台注册的应用程序](quickstart-remove-app.md)。

## <a name="application-manifest"></a>应用程序清单

旧体验和新体验对清单编辑器中的 JSON 格式使用不同的版本。 有关详细信息，请参阅 [Azure Active Directory 应用清单](reference-app-manifest.md)。

## <a name="new-ui"></a>新 UI

新体验为以下属性添加了 UI 控件：

- “身份验证”页包含“隐式授权流”(`oauth2AllowImplicitFlow`)。******** 与在旧体验中不同，现在可以启用“访问令牌”和/或“ID 令牌”。********
- “公开 API”页包含“此 API 定义的范围”(`oauth2Permissions`) 和“已授权的客户端应用程序”(`preAuthorizedApplications`)。************ 有关如何将应用配置为 Web API 并公开权限/作用域的详细信息，请参阅[快速入门：将应用程序配置为公开 Web API。](quickstart-configure-app-expose-web-apis.md)
- “品牌”页包含“发布者域”。******** 发布者域将在[应用程序的许可提示](application-consent-experience.md)中向用户显示。 有关详细信息，请参阅[如何：配置应用程序的发布者域](howto-configure-publisher-domain.md)。

## <a name="limitations"></a>限制

新体验具有以下限制：

- 客户端机密（应用密码）的格式不同于旧体验中的格式，可能会中断 CLI。
- 不支持在 UI 中更改受支持帐户的值。 除非在 Azure AD 单租户与多租户之间切换，否则需要使用应用清单。
