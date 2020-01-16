---
title: 将用于调用 web api 的 web API 移动到生产环境-Microsoft 标识平台 |Microsoft
description: 了解如何将用于调用 web Api 的 web API 移动到生产环境中。
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1426ad250e18d0132e116162a374120dda2e1200
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044132"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>用于调用 web Api 的 web API：移至生产环境

获取用于调用 web Api 的令牌后，可以将应用移动到生产环境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>了解更多

现在，你已了解如何从你自己的 web API 中调用 web Api 的基础知识，你可能对以下教程感兴趣，其中描述了用于生成可调用 web Api 的受保护 web API 的代码。

| 示例 | 平台 | Description |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API，Desktop （WPF） | ASP.NET Core 2.2 Web API 调用 Microsoft Graph，你可以使用 Microsoft 标识平台（v2.0）从 WPF 应用程序调用它。 |
