---
title: "使用 Azure 移动应用在 iOS 中添加身份验证"
description: "了解如何使用 Azure 移动应用通过各种标识提供者（包括 AAD、Google、Facebook、Twitter 和 Microsoft）对 iOS 应用的用户进行身份验证。"
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>Add authentication to your iOS app（将身份验证添加到 iOS 应用）
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本教程介绍如何使用支持的标识提供者向 [iOS 快速入门]项目添加身份验证。 本教程基于 [iOS 快速入门]教程，这是必须首先完成的教程。

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Xcode 中，按“运行”启动应用。 这样将引发异常，因为应用尝试以未经身份验证的用户身份访问后端，但 *TodoItem* 表现在要求身份验证。

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>向应用程序添加身份验证
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[iOS 快速入门]: app-service-mobile-ios-get-started.md

[Azure 门户]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


