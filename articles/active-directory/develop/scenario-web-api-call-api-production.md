---
title: 将调用 Web API 的 Web API 移到生产环境 - Microsoft 标识平台 | Azure
description: 了解如何将调用 Web API 的 Web API 移到生产环境。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: aea3a173fd07b66021d35142a84499ae9c66c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518192"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>调用 Web API 的 Web API：移到生产环境

获得调用 Web API 的令牌后，就可以将应用移到生产环境了。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>了解详细信息

既然你已了解了如何从自己的 Web API 调用 Web API 的基础知识，你可能会对下一个教程感兴趣，它介绍了用于构建调用 Web API 的受保护 Web API 的代码。

| 示例 | 平台 | 说明 |
|--------|----------|-------------|
| [aspnetcore-webapi-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph)第1章 | ASP.NET Core web API，桌面（WPF） | ASP.NET Core web API 调用 Microsoft Graph，你可以使用 Microsoft 标识平台（v2.0）从 WPF 应用程序调用它。 |
