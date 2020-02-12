---
title: 准备移动应用-为生产调用 web Api |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 web Api 的移动应用程序。 （准备用于生产的应用。）
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132390"
---
# <a name="prepare-mobile-apps-for-production"></a>为生产准备移动应用

本文详细介绍了如何提高移动应用的质量和可靠性，然后再将其移动到生产环境。

## <a name="handle-errors"></a>处理错误

在为生产准备移动应用时，可能会出现几个错误情况。 你将处理的主要情况是无提示故障，并回退到交互。 你应考虑的其他情况包括无网络情况、服务中断、管理员同意要求以及其他特定于方案的情况。

对于每个 Microsoft 身份验证库（MSAL）类型，可以找到说明如何处理错误条件的示例代码和 wiki 内容：

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>缓解并调查问题

为了更好地诊断应用中的问题，收集数据。 有关可以收集的数据类型的信息，请参阅[在 MSAL 应用程序中进行日志记录](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)。

以下是有关数据收集的一些建议：

- 用户可能会在遇到问题时询问帮助。 最佳做法是捕获并临时存储日志。 提供用户可在其中上传日志的位置。 MSAL 提供日志记录扩展来捕获有关身份验证的详细信息。

- 如果遥测可用，请通过 MSAL 启用该功能，收集有关用户如何登录到应用的数据。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

若要尝试其他示例，请参阅[桌面和移动公用客户端应用](sample-v2-code.md#desktop-and-mobile-public-client-apps)。
