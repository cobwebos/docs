---
title: Azure 应用服务中针对移动应用的身份验证和授权 | Microsoft Docs
description: Azure 应用服务的身份验证/授权功能的概念性参考和概述，尤其是针对移动应用
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 237310c607eb8488e53631b6e69d01703d1ebf99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Azure 应用服务中针对移动应用的身份验证和授权

本文介绍了在开发具有应用服务后端的原生移动应用时身份验证和授权功能如何工作。 应用服务提供了集成的身份验证和授权，因此，移动应用可以在不更改应用服务中的任何代码的情况下让用户登录。 该功能可以方便地保护应用程序和处理每个用户的数据。 

本文重点介绍了移动应用开发。 若要快速了解如何将应用服务身份验证和授权用于移动应用，请参阅以下教程之一：[向 iOS 应用添加身份验证][iOS]（或 [Android]、[Windows]、[Xamarin.iOS]、[Xamarin.Android]、[Xamarin.Forms] 或 [Cordova]）。 

有关应用服务中的身份验证和授权如何工作的信息，请参阅 [Azure 应用服务中的身份验证和授权](../app-service/app-service-authentication-overview.md)。

## <a name="authentication-with-provider-sdk"></a>在使用提供者 SDK 的情况下进行身份验证

在应用服务中配置所有项目后，可以将移动客户端修改为通过应用服务进行登录。 可以使用下述两种方式：

* 使用给定标识提供者发布的 SDK 来建立标识，即可获得应用服务的访问权限。
* 使用单行代码即可让移动应用客户端 SDK 登录用户。

> [!TIP]
> 大多数应用程序应使用提供者 SDK，这样可以让用户在登录时获得更一致的体验，可以使用令牌刷新支持，还可以获得提供者指定的其他权益。
> 
> 

使用提供者 SDK 时，用户一登录即可获得与操作系统结合更紧密的体验，而应用程序就运行在该操作系统中。 此方法还提供提供者令牌以及客户端上的某些用户信息，因此可以更轻松地使用图形 API 和自定义用户体验。 在博客和论坛上，此过程有时也被称为“客户端流”或“客户端定向流”，因为客户端代码可以登录用户，还可以访问提供者令牌。

获取提供者令牌后，需将其发送到应用服务进行验证。 应用服务在验证令牌后会创建新的应用服务令牌，将其返回给客户端。 移动应用客户端 SDK 提供的帮助器方法可用于管理此交换，并可自动将令牌附加到针对应用程序后端的所有请求。 开发人员也可以保留对提供者令牌的引用。

有关身份验证流的详细信息，请参阅[应用服务身份验证流](../app-service/app-service-authentication-overview.md#authentication-flow)。 

## <a name="authentication-without-provider-sdk"></a>在不使用提供者 SDK 的情况下进行身份验证

如果不希望设置提供者 SDK，可以利用 Azure 应用服务的移动应用功能进行登录。 移动应用客户端 SDK 会针对所选提供者打开一个 Web 视图，方便用户登录。 在博客和论坛上，此过程有时也被称为“服务器流”或“服务器定向流”，因为服务器管理用户登录过程，而客户端 SDK 从来不会收到提供者令牌。

启动此流程的代码包括在每个平台的身份验证教程中。 在流程结束时，客户端 SDK 将拥有一个应用服务令牌，该令牌自动附加到针对应用程序后端的所有请求。

有关身份验证流的详细信息，请参阅[应用服务身份验证流](../app-service/app-service-authentication-overview.md#authentication-flow)。 
## <a name="more-resources"></a>更多资源

以下教程展示了如何通过[服务器定向流](../app-service/app-service-authentication-overview.md#authentication-flow)向移动客户端添加身份验证：

* [将身份验证添加到 iOS 应用][iOS]
* [将身份验证添加到 Android 应用][Android]
* [将身份验证添加到 Windows 应用][Windows]
* [将身份验证添加到 Xamarin.iOS 应用][Xamarin.iOS]
* [将身份验证添加到 Xamarin.Android 应用][Xamarin.Android]
* [将身份验证添加到 Xamarin.Forms 应用][Xamarin.Forms]
* [将身份验证添加到 Cordova 应用][Cordova]

若要为 Azure Active Directory 使用[客户端定向流](../app-service/app-service-authentication-overview.md#authentication-flow)，请参阅以下资源：

* [使用适用于 iOS 的 Active Directory 身份验证库][ADAL-iOS]
* [使用适用于 Android 的 Active Directory 身份验证库][ADAL-Android]
* [使用适用于 Windows 和 Xamarin 的 Active Directory 身份验证库][ADAL-dotnet]

若要为 Facebook 使用[客户端定向流](../app-service/app-service-authentication-overview.md#authentication-flow)，请参阅以下资源：

* [使用 Facebook SDK for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

若要为 Twitter 使用[客户端定向流](../app-service/app-service-authentication-overview.md#authentication-flow)，请参阅以下资源：

* [使用 Twitter Fabric for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

若要为 Google 使用[客户端定向流](../app-service/app-service-authentication-overview.md#authentication-flow)，请参阅以下资源：

* [使用适用于 iOS 的 Google 登录 SDK](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
