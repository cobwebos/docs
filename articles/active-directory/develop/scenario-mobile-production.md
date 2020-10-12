---
title: 准备移动应用 - 为生产调用 Web API | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用。 （准备用于生产的应用。）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1a7407c947c1c5bae1eed00acb0f216722f3d09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121028"
---
# <a name="prepare-mobile-apps-for-production"></a>准备用于生产的移动应用

本文详细介绍了如何在将移动应用移到生产环境之前提高移动应用的质量和可靠性。

## <a name="handle-errors"></a>处理错误

在为生产准备移动应用时，可能会出现几个错误情况。 要处理的主要情况是无提示失败和回退到交互。 应考虑的其他情况包括无网络情况、服务中断、管理员同意的要求以及其他特定于场景的情况。

对于每个 Microsoft 身份验证库 (MSAL) 类型，可以找到说明如何处理错误情况的示例代码和 wiki 内容：

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>缓解和调查问题

若要更好地诊断应用中的问题，请收集数据。 有关可以收集的数据类型的信息，请参阅 [MSAL 应用程序中的日志记录](./msal-logging.md)。

一些数据收集建议如下：

- 用户在有问题时可能会寻求帮助。 最佳做法是捕获日志并将其临时存储。 提供一个供用户上传日志的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细信息。

- 如果遥测可用，请通过 MSAL 启用它，以收集有关用户如何登录应用的数据。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

若要尝试其他示例，请参阅[桌面和移动公共客户端应用](sample-v2-code.md#desktop-and-mobile-public-client-apps)。