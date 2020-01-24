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
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701971"
---
# <a name="scenario-protected-web-api"></a>方案：受保护的 web API

在此方案中，我们将向你展示如何公开 web API，以及如何对其进行保护，以便只有经过身份验证的用户才能访问 API。 你需要使用工作或学校帐户或个人 Microsoft 个人帐户来使用你的 web API 来启用经过身份验证的用户。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>细节

下面是一些您需要了解的有关保护 web Api 的细节：

- 应用注册必须公开至少一个作用域。 Web API 接受的令牌版本取决于登录访问群体。
- Web API 的代码配置必须验证在调用 web API 时使用的令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-protected-web-api-app-registration.md)
