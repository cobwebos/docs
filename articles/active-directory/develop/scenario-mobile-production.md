---
title: 调用 web Api （移动到生产环境）-Microsoft 标识平台的移动应用
description: 了解如何构建移动应用程序调用 web Api （移动到生产环境）
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc8b7c86495c2a60b07bef0a825e3872f787520
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550398"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>移动应用程序调用 web Api-移动到生产环境

本文提供了有关如何改进质量和可靠性的应用程序之前将其移到生产环境的详细信息。

## <a name="handling-errors-in-mobile-applications"></a>在移动应用程序中处理错误

此时将应用程序中可以发生大量的错误条件。 若要处理的主要方案是无提示的失败和交互的回退。 应考虑用于生产的其他条件包括无网络情况下、 服务中断，要求管理员同意和其他特定于方案的情况。

每个 MSAL 库都介绍了如何处理这些情况的示例代码和 wiki 内容：

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>缓解和调查的问题

若要诊断应用程序中的问题，最好先收集数据。 有关的数据类型的信息可以收集，请参阅 MSAL 平台 wiki。

- 在遇到问题时，用户可能会寻求帮助。 最佳做法是捕获和临时存储日志，并提供其中用户可以将其上传的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细的信息。
- 如果可用，则启用遥测数据通过 MSAL 来收集有关用户向应用程序的签名的数据。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
