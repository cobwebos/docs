---
title: 迁移到 Microsoft 身份验证库 （MSAL）
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身份验证库 （MSAL） 和 Azure AD 身份验证库 （ADAL） 之间的差异以及如何迁移到 MSAL。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164927"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>将应用程序迁移到 Microsoft 身份验证库 （MSAL）

Microsoft 身份验证库 （MSAL） 和 Azure AD 身份验证库 （ADAL） 都用于对 Azure AD 实体进行身份验证，并从 Azure AD 请求令牌。 截止目前，大多数开发人员都是通过 Azure AD 身份验证库 (ADAL) 来请求令牌，使用面向开发人员的 Azure AD 平台 (v1.0) 来对 Azure AD 标识（工作和学校帐户）进行身份验证。 使用 MSAL：

- 在使用 Microsoft 标识平台终结点时，可以通过 Azure AD B2C 对更广泛的 Microsoft 标识集（Azure AD 标识和 Microsoft 帐户以及社交和本地帐户）进行身份验证。
- 用户将获得最佳单一登录体验。
- 您的应用程序可以启用增量同意，并且支持条件访问更容易。
- 您从创新中获益。

**MSAL现在是推荐的auth库与微软身份平台一起使用**。 ADAL 上不会实现任何新功能。 工作的重点是改进 MSAL。

以下文章描述了 MSAL 和 ADAL 库之间的差异，并帮助您迁移到 MSAL：
- [迁移到 MSAL.NET](msal-net-migration.md)
- [迁移到 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [迁移到 MSAL.Android](migrate-android-adal-msal.md)
- [迁移到 MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [迁移到 MSAL Python](migrate-python-adal-msal.md)
- [迁移到适用于 Java 的 MSAL](migrate-adal-msal-java.md)
- [使用代理将 Xamarin 应用迁移到 MSAL.NET](msal-net-migration-ios-broker.md)
