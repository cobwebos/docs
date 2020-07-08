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
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 52a4a7131c85231107a2a23a1916016776b219fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367421"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>将应用程序迁移到 Microsoft 身份验证库 (MSAL)

许多开发人员都使用 Azure Active Directory 身份验证库（ADAL）来构建和部署应用程序。 我们现在建议使用 Microsoft 身份验证库（MSAL）进行身份验证和授权 Azure AD 实体。

使用 MSAL 而不是 ADAL：

- 你可以对一组更广泛的标识进行身份验证：
  - Azure AD 标识
  - Microsoft 帐户
  - 使用 Azure AD B2C 的社交和本地帐户
- 用户将获得最佳单一登录体验。
- 应用程序可以启用增量许可。
- 支持条件性访问更容易。
- 你可以受益于创新。 由于所有 Microsoft 开发工作现在都专注于 MSAL，因此不会在 ADAL 中实现任何新功能。

**MSAL 现在是推荐用于 Microsoft 标识平台的身份验证库**。

## <a name="migration-guidance"></a>迁移指导

以下文章可帮助你迁移到 MSAL：

- [迁移到 MSAL.Android](migrate-android-adal-msal.md)
- [迁移到 MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [迁移到 MSAL Java](migrate-adal-msal-java.md)
- [迁移到 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [迁移到 MSAL.NET](msal-net-migration.md)
- [迁移到 MSAL Python](migrate-python-adal-msal.md)
- [使用代理将 Xamarin 应用迁移到 MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

__问：是否弃用 ADAL？__  
A:是的。 从2020年6月30日开始，我们将不再向 ADAL 添加新功能。 我们将继续在2022年6月30日之前将重要的安全修补程序添加到 ADAL。

__问：如何实现知道哪些应用正在使用 ADAL？__  
答：如果你有应用程序的源代码，则可以参考上述迁移指南来帮助确定应用使用的库，以及如何将其迁移到 MSAL。 如果无权访问应用程序的源代码，可以[打开支持请求](developer-support-help-options.md#open-a-support-request)，获取已注册应用程序和每个应用程序使用的库的列表。

__问：我的现有 ADAL 应用是否会继续工作？__  
答：你的现有应用程序将继续运行而不进行修改。 如果计划将其保留在2022年6月30日之前，你应该考虑将其更新为 MSAL 以确保它们的安全性，但不需要迁移到 MSAL 来维护现有功能。

__问：为什么应投入到 MSAL？__  
答： MSAL 包含不在 ADAL 中的新功能，包括增量许可、单一登录和令牌缓存管理。 另外，与 ADAL 不同，MSAL 将继续接收2022年6月30日之前的安全修补程序。 [了解详细信息](msal-overview.md)。

__问：你是否会发布一种工具，用于帮助我将我的应用从 ADAL 移到 MSAL？__  
答：否。 库之间的差异需要专用于开发和维护工具的资源，否则会用到改进 MSAL 的工具。 但是，我们提供了前面的一组迁移指南来帮助你在应用程序中进行所需的更改。

__问： MSAL 如何与 AD FS 一起使用？__  
答： MSAL.NET 支持针对 AD FS 2019 进行身份验证的某些方案。 如果你的应用程序需要直接从 AD FS 的早期版本获取令牌，则你应保留在 ADAL 上。 [了解详细信息](msal-net-adfs-support.md)。

__问：如何实现获取有关迁移应用程序的帮助吗？__  
答：请参阅本文中的[迁移指南](#migration-guidance)部分。 如果在阅读应用平台的指南后，你有更多问题，你可以使用该标记发布 Stack Overflow， `[adal-deprecation]` 或在库的 GitHub 存储库中提出问题。 有关指向每个库存储库的链接，请参阅 MSAL 概述一文的 "[语言和框架](msal-overview.md#languages-and-frameworks)" 一节。

## <a name="next-steps"></a>后续步骤

- [更新应用程序以使用 Microsoft 身份验证库和 Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [了解有关 Microsoft 标识平台的详细信息（MSAL）](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [查看我们的 MSAL 代码示例](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)
