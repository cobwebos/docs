---
title: 受保护的 web API-概述
titleSuffix: Microsoft identity platform
description: 了解如何构建受保护的 Web API（概述）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026622"
---
# <a name="scenario-protected-web-api"></a>方案：受保护的 Web API

通过此方案了解如何公开 Web API。 还了解如何保护 Web API，以便只有经过身份验证的用户才能访问它。

若要使用你的 web API，你需要启用使用工作帐户和学校帐户的经过身份验证的用户，或启用 Microsoft 个人帐户。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>详情

下面是保护 Web API 时需要了解的具体信息：

- 应用注册必须公开至少一个*作用域*或一个*应用程序角色*。
  - 范围由代表用户调用的 web Api 公开。
  - 应用程序角色由守护程序应用程序调用的 web Api 公开，后者代表自己调用 web API。
- 如果创建新的 web API 应用注册，请选择 web API 接受的[访问令牌版本](reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` 。 对于旧版 web Api，接受的令牌版本可以为 `null` ，但此值会将登录受众限制为仅限组织，而不支持个人 Microsoft 帐户（MSA）。
- Web API 的代码配置必须验证调用 Web API 时使用的令牌。
- 控制器操作中的代码必须验证令牌中的角色或作用域。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-protected-web-api-app-registration.md)
