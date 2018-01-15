---
title: "Azure 移动应用中的身份验证和授权 | Microsoft Docs"
description: "Azure 移动应用身份验证/授权功能的概念参考和概述"
services: app-service\mobile
documentationcenter: 
author: mattchenderson
manager: cfowler
editor: 
ms.assetid: a46dbf70-867d-48f6-8885-7f5207ad102e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 90c11b09351f019c45f5f1b025d67947b69b3b0a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>Azure 移动应用中的身份验证和授权
## <a name="what-is-app-service-authentication--authorization"></a>什么是应用服务身份验证/授权？
> [!NOTE]
> 本主题将移入[应用服务身份验证/授权](../app-service/app-service-authentication-overview.md)合并主题，其中包括 Web 应用、移动应用和 API 应用。
> 
> 

应用服务身份验证/授权是一项功能，使应用程序能够将用户登录，而无需在应用后端进行任何代码更改。 该功能可以方便地保护应用程序和处理每个用户的数据。

应用服务使用联合标识，第三方**标识提供者**（“IDP”）在其中存储帐户和验证用户，应用程序使用此标识而不是自身的标识。 应用服务支持现有的五个标识提供者：Azure Active Directory、Facebook、Google、Microsoft 帐户和 Twitter。 也可以通过集成其他标识提供者或自定义的标识解决方案，为应用扩展此项支持。

应用可以利用其中任意数目的标识提供者，为最终用户提供登录方式选项。

如果想要立即入门，请参阅以下教程之一：

* [将身份验证添加到 iOS 应用]
* [将身份验证添加到 Xamarin.iOS 应用]
* [将身份验证添加到 Xamarin.Android 应用]
* [将身份验证添加到 Windows 应用]

## <a name="how-authentication-works"></a>身份验证的工作原理
若要使用其中某个标识提供者进行身份验证，首先需对标识提供者进行配置，使之了解应用程序。 然后，标识提供者向用户提供 ID 和机密，再由用户将其提供给应用程序。 这样就构成了信任关系，使应用服务能够验证收到的标识。

以下主题详细说明了这些步骤：

* [如何将应用配置为使用 Azure Active Directory 登录]
* [如何将应用配置为使用 Facebook 登录]
* [如何将应用配置为使用 Google 登录]
* [如何将应用配置为使用 Microsoft 帐户登录]
* [如何将应用配置为使用 Twitter 登录]

在后端将一切配置准备就绪后，可以修改用于登录的客户端。 可以使用下述两种方式：

* 使用一行代码，让移动应用客户端 SDK 登录用户。
* 利用给定标识提供者发布的 SDK 建立标识，并获取对应用服务的访问权限。

> [!TIP]
> 大多数应用程序都应该使用提供程序 SDK 来获得更自然的登录体验，使用刷新支持和其他提供程序特定的优势。
> 
> 

### <a name="how-authentication-without-a-provider-sdk-works"></a>不使用提供程序 SDK 进行身份验证的工作原理
如果不想要设置提供程序 SDK，可以允许移动应用自动执行登录。 移动应用客户端 SDK 将为所选的提供程序打开一个 Web 视图，然后完成登录。 在博客和论坛上，偶尔也会将这种方式称为“服务器流”或“服务器定向流”，因为服务器管理登录，而客户端 SDK 永远不会收到提供程序令牌。

适用于每个平台的身份验证教程中提供了启动此流所需的代码。 在流程结束时，客户端 SDK 将拥有一个应用服务令牌，该令牌自动附加到针对后端的所有请求。

### <a name="how-authentication-with-a-provider-sdk-works"></a>使用提供程序 SDK 进行身份验证的工作原理
使用提供程序 SDK 可让登录体验与应用运行所在的平台 OS 更紧密地交互。 这同时还提供提供者令牌以及客户端上的某些用户信息，因此可以更轻松地使用图形 API 和自定义用户体验。 在博客和论坛上，偶尔也会看到此过程被称为“客户端流”或“客户端定向流”，因为客户端代码处理登录，还可以访问提供程序令牌。

获取提供者令牌后，需将其发送到应用服务进行验证。 在流程结束时，客户端 SDK 将拥有一个应用服务令牌，该令牌自动附加到针对后端的所有请求。 开发人员也可选择性地保留对提供者令牌的引用。

## <a name="how-authorization-works"></a>授权工作原理
应用服务身份验证/授权公开**请求未经身份验证时需执行的操作**的多个选项。 在代码收到给定的请求之前，可以进行应用服务检查来查看是否对请求进行身份验证，如果没有，则拒绝请求并尝试让用户登录，并再试一次。

其中一个选项是让未经身份验证的请求重定向到其中一个标识提供者。 在 Web 浏览器中，这实际上会将用户转到新页面。 但是，这样就无法重定向移动客户端，并且未经身份验证的响应将收到 HTTP“401 未授权”响应。 考虑到这一点，客户端发出的第一个请求应始终为登录终结点，并可对任何其他 API 进行调用。 如果尝试在登录之前调用另一个 API，客户端将收到错误。

如果想要更精细控制哪些终结点需要身份验证，也可以针对未经身份验证的请求选择“无操作(允许请求)”。 在此情况下，所有身份验证决策都将延后到应用程序代码。 也可以根据自定义的授权规则来允许特定的用户访问。

## <a name="documentation"></a>文档
以下教程说明如何使用应用服务将身份验证添加到移动客户端：

* [将身份验证添加到 iOS 应用]
* [将身份验证添加到 Xamarin.iOS 应用]
* [将身份验证添加到 Xamarin.Android 应用]
* [将身份验证添加到 Windows 应用]

以下教程说明了如何将应用服务配置为使用不同的身份验证提供程序：

* [如何将应用配置为使用 Azure Active Directory 登录]
* [如何将应用配置为使用 Facebook 登录]
* [如何将应用配置为使用 Google 登录]
* [如何将应用配置为使用 Microsoft 帐户登录]
* [如何将应用配置为使用 Twitter 登录]

如果想要使用此处提供的标识系统以外的标识系统，也可以利用 [.NET 服务器 SDK 中的预览自定义身份验证](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth)支持。

[将身份验证添加到 iOS 应用]: app-service-mobile-ios-get-started-users.md
[将身份验证添加到 Xamarin.iOS 应用]: app-service-mobile-xamarin-ios-get-started-users.md
[将身份验证添加到 Xamarin.Android 应用]: app-service-mobile-xamarin-android-get-started-users.md
[将身份验证添加到 Windows 应用]: app-service-mobile-windows-store-dotnet-get-started-users.md

[如何将应用配置为使用 Azure Active Directory 登录]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[如何将应用配置为使用 Facebook 登录]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[如何将应用配置为使用 Google 登录]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[如何将应用配置为使用 Microsoft 帐户登录]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[如何将应用配置为使用 Twitter 登录]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
