---
title: 受保护的 Web API-概述
titleSuffix: Microsoft identity platform
description: 了解如何构建受保护的 web API （概述）。
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773336"
---
# <a name="scenario-protected-web-api"></a>方案：受保护的 web API

在此方案中，你将了解如何公开 web API。 你还将了解如何保护 web API，以便只有经过身份验证的用户可以访问它。

若要使用你的 web API，你需要启用使用工作帐户和学校帐户的经过身份验证的用户，或启用 Microsoft 个人帐户。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>细节

下面是保护 web Api 需要了解的特定信息：

- 应用注册必须公开至少一个作用域。 Web API 接受的令牌版本取决于登录访问群体。
- Web API 的代码配置必须验证调用 web API 时使用的令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-protected-web-api-app-registration.md)
