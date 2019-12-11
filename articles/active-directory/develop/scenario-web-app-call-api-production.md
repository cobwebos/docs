---
title: 将 web 应用程序调用 web Api 移动到生产环境-Microsoft 标识平台 |Microsoft
description: 了解如何将用于调用 web Api 的 web 应用移动到生产环境。
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
ms.openlocfilehash: e40920b7045dad6f64b5699bc3c4c560eb399f2a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964950"
---
# <a name="web-app-that-calls-web-apis---move-to-production"></a>用于调用 web Api 的 web 应用-转到生产

了解如何获取令牌来调用 Web Api 后，请了解如何迁移到生产。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅完整的 ASP.NET Core web 应用渐进教程，其中显示：

- 如何用多个受众、国家云和社交标识登录用户
- 调用 Microsoft Graph
- 调用多个 Microsoft Api
- 处理增量同意
- 调用你自己的 Web API

> [!div class="nextstepaction"]
> [ASP.NET Core web 应用教程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
