---
title: 准备移动应用调用 Web API 进行生产 |蔚蓝
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用。 （为生产准备应用。
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
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132390"
---
# <a name="prepare-mobile-apps-for-production"></a>准备移动应用进行生产

本文详细介绍了如何在将移动应用移动到生产之前提高移动应用的质量和可靠性。

## <a name="handle-errors"></a>处理错误

在为生产准备移动应用时，可能会出现多个错误条件。 您将处理的主要情况是静默故障和对交互的回退。 应考虑的其他条件包括无网络情况、服务中断、管理员同意要求以及其他特定于方案的情况。

对于每种 Microsoft 身份验证库 （MSAL） 类型，您可以找到描述如何处理错误条件的示例代码和 wiki 内容：

- [MSAL 安卓维基](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET维基](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>缓解和调查问题

为了更好地诊断应用中的问题，请收集数据。 有关可以收集的数据类型的信息，请参阅[在 MSAL 应用程序中登录](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)。

以下是数据收集的一些建议：

- 当用户遇到问题时，他们可能会寻求帮助。 最佳做法是捕获和临时存储日志。 提供用户可以上载日志的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细信息。

- 如果遥测可用，请通过 MSAL 使其收集有关用户如何登录到你的应用的数据。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

要尝试其他示例，请参阅[桌面和移动公共客户端应用](sample-v2-code.md#desktop-and-mobile-public-client-apps)。
