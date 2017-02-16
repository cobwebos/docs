---
title: "Azure Active Directory B2C：从 Android 应用程序调用 Web API | Microsoft 文档"
description: "本文说明如何创建一个可以使用 OAuth 2.0 持有者令牌调用 Node.js Web API 的 Android“待办事项列表”应用。 该 Android 应用和 Web API 都使用 Azure Active Directory B2C 来管理用户标识和验证用户身份。"
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C：从 Android 应用程序调用 Web API
> [!WARNING]
> 我们已发布更新的 Android 代码示例，可在[此处](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi)找到。  本示例使用一个第三方库，该库经测试可与 Azure AD B2C 的基本方案兼容。  此库利用嵌入式 Web 视图而不是系统浏览器。  Google [宣布](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)在 2017 年 4 月 20 日不再支持使用嵌入式 Web 视图执行 Google 帐户登录，到时，想要支持 Google 帐户的用户需要更新库。  

>Microsoft 不提供第三方库的修复程序，且尚未审查这些库。 问题和功能请求应重定向到库的开源项目。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>




<!--HONumber=Feb17_HO1-->


