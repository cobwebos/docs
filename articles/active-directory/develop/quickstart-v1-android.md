---
title: 从 Android 应用 (v1.0) 登录用户并调用 Microsoft Graph API | Microsoft Docs
description: 了解如何从 Android 应用登录用户并调用 Microsoft Graph API。
services: active-directory
documentationcenter: android
author: rwike77
manager: CelesteDG
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da92944149f983960605bd219ce9707bb3e9276
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150278"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app-v10"></a>快速入门：从 Android 应用 (v1.0) 登录用户并调用 Microsoft Graph API

[Microsoft 标识平台](v2-overview.md)由 Azure Active Directory (Azure AD) 开发人员平台演变而来。 开发人员可以通过它来生成应用程序，从而可以采用所有 Microsoft 标识登录，以及获取令牌来调用 Microsoft Graph 等 Microsoft API 或开发人员生成的 API。

借助 [Microsoft 身份验证库 (MSAL)](msal-overview.md)，开发人员能够从 Microsoft 标识平台终结点获取令牌，以访问受保护的 Web API。 Active Directory 身份验证库 (ADAL) 与适用于开发人员的 Azure AD (v1.0) 终结点集成，其中 MSAL 与 Microsoft 标识平台 (v2.0) 终结点集成。

## <a name="next-steps"></a>后续步骤

对于新的 Android 应用程序，建议使用 Microsoft 标识平台 (v2.0) 和 MSAL 来获取令牌并访问受保护的 Web API。 请参阅[快速入门：从 Android 应用](quickstart-v2-android.md)登录用户并调用 Microsoft Graph API，即可开始使用。
