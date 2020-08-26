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
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ac9264ea8d6cc71d19d2c9bbd23b2123bdf1f924
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224350"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>将应用程序迁移到 Microsoft 身份验证库 (MSAL)

许多开发人员都使用 Azure Active Directory 身份验证库 (ADAL) 来构建和部署应用程序。 我们现在建议使用 Microsoft 身份验证库 (MSAL) 对 Azure AD 实体进行身份验证和授权。

通过使用 MSAL 而不是 ADAL：

- 你可以对一组更广泛的标识进行身份验证：
  - Azure AD 标识
  - Microsoft 帐户
  - 使用 Azure AD B2C 的社交和本地帐户
- 用户将获得最佳单一登录体验。
- 应用程序可以启用增量许可。
- 可以更轻松地支持条件访问。
- 你将从创新中受益。 因为 Microsoft 所有的开发工作现在都集中在 MSAL 上，所以不会在 ADAL 中实现新功能。

**MSAL 现在是建议用于 Microsoft 标识平台的身份验证库**。

## <a name="migration-guidance"></a>迁移指南

可参考以下文章迁移到 MSAL：

- [迁移到 MSAL.Android](migrate-android-adal-msal.md)
- [迁移到 MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [迁移到 MSAL Java](migrate-adal-msal-java.md)
- [迁移到 MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [迁移到 MSAL.NET](msal-net-migration.md)
- [迁移到 MSAL Python](migrate-python-adal-msal.md)
- [使用代理将 Xamarin 应用迁移到 MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

__问：是否要弃用 ADAL？__  
答：是的。 从 2020 年 6 月 30 日开始，我们将不再为 ADAL 添加新功能。 在 2022 年 6 月 30 日之前，我们将继续为 ADAL 添加关键的安全修复程序。 在此日期之后，使用 ADAL 的应用程序将继续工作，但我们建议升级到 MSAL，以利用最新功能并保持安全。

__问：我的现有 ADAL 应用是否会停止工作？__  
答：否。 你的现有应用程序将继续运行而不进行修改。 如果计划将其保存在2022年6月30日之前，应考虑将应用程序更新为 MSAL 以确保它们的安全性，但不需要迁移到 MSAL 来维护现有功能。

__问：如何知道哪些应用正在使用 ADAL？__  
答：如果你有应用程序的源代码，可以参考上述迁移指南来确定应用使用的库和了解如何将其迁移到 MSAL。 如果你与 ISV 合作，我们建议你直接访问这些资源，以了解迁移到 MSAL 的过程。

__问：为什么应设法迁移到 MSAL？__  
答：MSAL 包含 ADAL 中没有的新功能，包括增量许可、单一登录和令牌缓存管理。 此外，与 ADAL 不同，MSAL 在 2022 年 6 月 30 日之后会继续接收安全补丁。 [了解详细信息](msal-overview.md)。

__问： Microsoft 是否会将其自己的应用更新到 MSAL？__  
是的。 Microsoft 正在根据支持截止时间将其应用程序迁移到 MSAL，确保它们将从 MSAL 的持续安全和功能改进中受益。

__问：是否会发布一种工具，来帮助我将应用从 ADAL 迁移到 MSAL？__  
答：否。 由于库之间存在差异，需要专门投入资源来开发和维护这个工具，而这些资源本可用于改进 MSAL。 但是，我们在前面提供了一组迁移指南，可帮助你对应用程序进行所需的更改。

__问：MSAL 如何与 AD FS 配合工作？__  
答：MSAL.NET 支持针对 AD FS 2019 进行身份验证的特定方案。 如果你的应用需要直接从 AD FS 的早期版本获取令牌，应继续使用 ADAL。 [了解详细信息](msal-net-adfs-support.md)。

__问：如何获取有关迁移应用程序的帮助？__  
答：请参阅本文的[迁移指南](#migration-guidance)部分。 如果在阅读了应用平台的指南后，你还有其他问题，可以使用标记 `[adal-deprecation]` 在 Stack Overflow 上发布问题，或者在库的 GitHub 存储库中提交问题。 请参阅 MSAL 概述文章的[语言和框架](msal-overview.md#languages-and-frameworks)部分，获取指向每个库的存储库的链接。

## <a name="next-steps"></a>后续步骤

- [更新应用程序以使用 Microsoft 身份验证库和 Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Microsoft 标识平台概述](v2-overview.md)
- [查看我们的 MSAL 代码示例](sample-v2-code.md)
