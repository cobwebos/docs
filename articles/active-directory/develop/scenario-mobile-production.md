---
title: 将移动应用调用 web Api 移动到生产-Microsoft 标识平台 |Microsoft
description: 了解如何构建调用 web Api 的移动应用（转到生产环境）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1a82fc7dc1b18fa21657170af29f7de7e84d7c1f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702022"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>用于调用 web Api 的移动应用-迁移到生产

本文详细介绍了如何提高应用程序的质量和可靠性，然后再将其移动到生产环境。

## <a name="handling-errors-in-mobile-applications"></a>处理移动应用程序中的错误

此时，你的应用程序中可能会出现很多错误情况。 要处理的主要方案是无提示故障，并回退到交互。 对于生产，你应该考虑的其他情况包括无网络情况、服务中断、管理员同意要求以及其他特定于方案的情况。

每个 MSAL 库都包含示例代码和 wiki 内容，用于说明如何处理这些情况：

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>缓解并调查问题

若要诊断应用中的问题，有助于收集数据。 有关可以收集的数据类型的信息，请参阅 MSAL platform wiki。

- 用户在遇到问题时可能会要求提供帮助。 最佳做法是捕获并临时存储日志，并提供一个可供用户上传的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细信息。
- 如果可用，请启用通过 MSAL 的遥测，收集有关用户登录到应用的方式的数据。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

试用示例中提供的其他示例[|桌面和移动公用客户端应用](sample-v2-code.md#desktop-and-mobile-public-client-apps)
