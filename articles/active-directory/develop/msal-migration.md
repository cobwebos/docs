---
title: 迁移到 Microsoft 身份验证库 (MSAL)
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 身份验证库 (MSAL) 与 Azure AD 身份验证库 (ADAL) 之间的差异，以及如何迁移到 MSAL。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164927"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>将应用程序迁移到 Microsoft 身份验证库 (MSAL)

Microsoft 身份验证库 (MSAL) 与 Azure AD 身份验证库 (ADAL) 用于对 Azure AD 实体进行身份验证，以及从 Azure AD 请求令牌。 截止目前，大多数开发人员都是通过 Azure AD 身份验证库 (ADAL) 来请求令牌，使用面向开发人员的 Azure AD 平台 (v1.0) 来对 Azure AD 标识（工作和学校帐户）进行身份验证。 使用 MSAL：

- 可以对更广泛的一组 Microsoft 标识进行身份验证（通过 Azure AD B2C 验证 Azure AD 标识和 Microsoft 帐户，以及社交和本地帐户），因为它使用 Microsoft 标识平台终结点。
- 用户将获得最佳单一登录体验。
- 应用程序可以启用增量许可，可以更轻松地为条件访问提供支持。
- 你将从创新中受益。

**MSAL 现在是建议用于 Microsoft 标识平台的身份验证库**。 我们不会对 ADAL 实现任何新的功能。 工作的重点是改进 MSAL。

以下文章介绍了 MSAL 和 ADAL 库之间的差异，并帮助你迁移到 MSAL：
- [迁移到 MSAL.NET](msal-net-migration.md)
- [迁移到 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [迁移到 MSAL.Android](migrate-android-adal-msal.md)
- [迁移到 MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [迁移到 MSAL Python](migrate-python-adal-msal.md)
- [迁移到适用于 Java 的 MSAL](migrate-adal-msal-java.md)
- [使用代理将 Xamarin 应用迁移到 MSAL.NET](msal-net-migration-ios-broker.md)
