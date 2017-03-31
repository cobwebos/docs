---
title: "Azure Active Directory B2C：局限和限制 | Microsoft Docs"
description: "Azure Active Directory B2C 的局限和限制列表"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: bryanla
ms.assetid: 04ec3310-98bb-4bb1-856d-ddc66913b390
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 610b72f9f979f76d2d9c5180cb51ddae82a3e823
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory B2C：局限和限制

尚不支持 Azure Active Directory (Azure AD) B2C 的部分功能。 许多这些已知的问题和局限将在之后被解决，但是如果你使用 Azure AD B2C 构建面向使用者的应用程序，则应该了解这些问题和限制。

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>创建 Azure AD B2C 租户期间遇到的问题

如果在[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)期间遇到问题，请参阅[创建 Azure AD 租户或 Azure AD B2C 租户 - 问题和解决方案](active-directory-b2c-support-create-directory.md)以获取指导。

请注意，当删除现有 B2C 租户并重新创建具有相同域名的 B2C 租户时，存在已知问题。 你必须创建具有不同域名的 B2C 租户。

## <a name="branding-issues-on-verification-email"></a>验证电子邮件中的品牌问题

默认验证电子邮件包含 Microsoft 品牌。 将来我们会删除它。 现在，你可以使用[公司品牌功能](../active-directory/active-directory-add-company-branding.md)将其删除。

## <a name="branding-issues-on-local-account-sign-in-page-in-a-sign-in-policy"></a>有关登录策略中本地帐户登录页的品牌问题

只能使用[公司品牌功能](../active-directory/active-directory-add-company-branding.md)来自定义登录策略中的本地帐户登录页，而不能使用[此处](active-directory-b2c-reference-ui-customization.md)所述的页面 UI 自定义功能进行自定义。 此外，用户名和密码字段中不会提供标签或占位符。 作为解决方法，我们建议改用完全可自定义的“注册或登录策略”。 如果想要完全自定义登录策略中的本地帐户登录页，请在 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13062033-b2c-fully-customizable-sign-in-page) 中为该功能投票。

## <a name="restrictions-on-applications"></a>对应用程序的限制

Azure AD B2C 中目前不支持以下类型的应用程序。 有关支持的应用程序类型的说明，请参阅 [Azure AD B2C：应用程序类型](active-directory-b2c-apps.md)。

### <a name="daemons--server-side-applications"></a>守护程序/服务器端应用程序

包含长时运行进程或不需要用户操作的应用程序还需要通过其他方法访问受保护的资源，例如 Web API。 这些应用程序可以在 [OAuth 2.0 客户端凭据流](active-directory-b2c-reference-protocols.md)中使用应用程序的标识（而不是使用者的委派标识）来进行身份验证和获取令牌。 此流尚不可用于 Azure AD B2C 中，到目前为止，应用程序仅能在发生交互式使用者登录流之后才能获取令牌。


### <a name="web-api-chains-on-behalf-of"></a>Web API 链（代理）

许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 Azure AD B2C 的保护。 此方案常见于具有 Web API 后端的本机客户端，该后端将调用 Azure AD B2C 图形 API 等 Microsoft Online 服务。

可以使用 OAuth 2.0 Jwt 持有者凭据授权（也称为代理流）来支持这种链接的 Web API 方案。 但是，Azure AD B2C 中目前尚未实现代理流。

## <a name="restrictions-on-reply-urls"></a>对回复 URL 的限制

在 Azure AD B2C 中注册的应用目前仅限使用一组有限的回复 URL 值。 Web 应用和服务的回复 URL 必须以方案 `https` 开头，并且所有回复 URL 值必须共享一个 DNS 域。 例如，无法注册具有以下定向 URL 的 Web 应用：

`https://login-east.contoso.com`
`https://login-west.contoso.com`

注册系统会将现有回复 URL 的完整 DNS 名称与要添加的回复 URL 的 DNS 名称相比较。 如果满足以下任一条件，添加 DNS 名称的请求将失败：

* 新回复 URL 的完整 DNS 名称与现有回复 URL 的 DNS 名称不匹配。
* 新回复 URL 的完整 DNS 名称不是现有回复 URL 的子域。

例如，如果应用具有以下回复 URL：

`https://login.contoso.com`

你可以向其添加，如下所示：

`https://login.contoso.com/new`

在这种情况下，DNS 名称将完全匹配。 或者，你可以执行下面的操作：

`https://new.login.contoso.com`

在这种情况下，你将引用 login.contoso.com 的 DNS 子域。 如果希望应用使用 login-east.contoso.com 和 login-west.contoso.com 作为回复 URL，必须按以下顺序添加这些回复 URL：

`https://contoso.com`
`https://login-east.contoso.com`
`https://login-west.contoso.com`

可以添加后两个回复 URL，因为它们是第一个回复 URL (contoso.com) 的子域。 即将发布的版本中将取消此限制。

若要了解如何在 Azure AD B2C 中注册应用，请参阅[如何将应用程序注册到 Azure Active Directory B2C](active-directory-b2c-app-registration.md)。

## <a name="restriction-on-libraries-and-sdks"></a>对库和 SDK 的限制

目前能使用 Azure AD B2C 的 Microsoft 支持的库非常有限。 我们支持基于 .NET 的 Web 应用和服务，以及 NodeJS Web 应用和服务。  我们还有一个名为 MSAL 的预览 .NET 客户端库，可与 Windows 和其他 .NET 应用中的 Azure AD B2C 共同使用。

目前没有支持任何其他语言或平台（包括 iOS 和 Android）的库。  如果你希望在与上述不同的平台上进行构建，建议你使用开放源代码 SDK，如有需要请参阅 [OAuth 2.0 和 OpenID Connect 协议参考](active-directory-b2c-reference-protocols.md)。  Azure AD B2C 实现了 OAuth 和 OpenID Connect，这使得可以使用通用 OAuth 或OpenID Connect 库进行集成。

我们的 iOS 和 Android 快速入门教程使用已测试与 Azure AD B2C 的兼容性的开源库。  在[入门](active-directory-b2c-overview.md#get-started)部分中提供了所有的快速入门教程。

## <a name="restriction-on-protocols"></a>对协议的限制

Azure AD B2C 支持 OpenID Connect 和 OAuth 2.0。 但是，并非每个协议的所有特性与功能都已实现。 若要进一步了解 Azure AD B2C 支持的协议功能范围，请阅读 [OpenID Connect 和 OAuth 2.0 协议参考](active-directory-b2c-reference-protocols.md)。 SAML 和 WS-Fed 协议支持不可用。

## <a name="restriction-on-tokens"></a>对令牌的限制

Azure AD B2C 颁发的许多令牌都实现为 JSON Web 令牌或 JWT。 但是，并不是 JWT（被称为“声明”）中包含的所有信息都应有的或者缺失的。 “preferred_username”声明就是一个例子。  随着时间推移，声明的值、格式或含义会发生更改，现有策略的令牌将不受影响，你可以依赖其在生产应用中的值。  在值更改时，我们将为你提供为每个策略配置这些更改的机会。  若要更好地了解 Azure AD B2C 服务当前发出的令牌，请阅读[令牌参考](active-directory-b2c-reference-tokens.md)。

## <a name="restriction-on-nested-groups"></a>对嵌套组的限制

Azure AD B2C 租户中不支持嵌套组成员身份。 我们不计划添加此功能。

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>对 Azure AD 图形 API 上的差异查询功能的限制

Azure AD B2C 租户中不支持 [Azure AD 图形 API 上的差异查询功能](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query)。 此功能在我们的长期计划中。

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Azure 经典门户上的用户管理问题

可以在 Azure 门户上访问 B2C 功能。 同时，也可以使用 Azure 经典门户来访问其他租户功能，包括用户管理。 目前在 Azure 经典门户上有几个已知的用户管理问题（“用户”选项卡）：

* 对于本地帐户用户（即使用电子邮件地址和密码或用户名和密码注册的使用者），“用户名”字段不对应于在注册过程中使用的登录标识符（电子邮件地址或用户名）。 这是因为 Azure 经典门户上显示的字段实际上是用户主体名称 (UPN)，该名称没有在 B2C 方案中使用。 若要查看本地帐户的登录标识符，请在 [Graph 浏览器](https://graphexplorer.cloudapp.net/)中找到用户对象。 你会发现与社交帐户用户（即，注册 Facebook、Google+ 等的使用者）相同的问题，但在这种情况下，没有登录标识符。

    ![本地帐户 - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)
* 对于本地帐户用户，将无法在“配置文件”选项卡中修改任何字段并保存更改。

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Azure 经典门户上管理员启动的密码重置问题

如果你在 Azure 经典门户上重置了基于本地帐户的使用者密码（“用户”选项卡上的“重置密码”命令），该使用者将无法在下次登录时更改他/她的密码（如果你使用注册或登录策略），并且将被锁定在应用程序之外。 作为解决方法，请使用 [Azure AD 图形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 重置使用者的密码（无密码过期）或使用登录策略而不是注册或登录策略。

## <a name="issues-with-creating-a-custom-attribute"></a>创建自定义属性的问题

[在 Azure 门户上添加的自定义属性](active-directory-b2c-reference-custom-attr.md)不会立即创建在你的 B2C 租户中。 你必须在至少一个策略中使用自定义属性，才能在 B2C 租户中创建它，并可以通过图形 API 使用。

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Azure 经典门户上验证域的问题

目前，无法在 [Azure 经典门户](https://manage.windowsazure.com/)上成功验证域。

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Safari 浏览器上使用 MFA 策略登录的问题

在具有 HTTP 400（错误请求）错误的 Safari 浏览器上，登录策略（已启用 MFA ）的请求会间歇性失败。 这是因为 Safari 的 Cookie 大小限制。 此问题有几种解决方法：

* 使用“注册或登录策略”代替“登录策略”。
* 减少政策中请求的**应用程序声明**的数量。

## <a name="issues-with-windows-desktop-wpf-apps-using-azure-ad-b2c"></a>使用 Azure AD B2C 的 Windows 桌面 WPF 应用的问题

从 Windows 桌面 WPF 应用向 Azure AD B2C 发出的请求有时失败并返回以下错误消息：“基于浏览器的身份验证对话未能完成。 原因: 协议未知，并且未输入匹配的可插入协议。”

原因在于 Azure AD B2C 提供的授权代码大小；该大小与令牌中请求的声明数相关。 此问题的解决方法之一是减少令牌中请求的声明数，并单独针对其他声明查询图形 API。

