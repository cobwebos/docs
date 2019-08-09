---
title: 受保护的 Web API - 概述 | Azure
description: 了解如何构建受保护的 Web API（概述）。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852578"
---
# <a name="scenario-protected-web-api"></a>方案:受保护的 Web API

在此方案中，我们将展示如何公开 Web API 以及如何对其进行保护，以便只有经过身份验证的用户才能访问该 API。 你需要使用工作或学校帐户或个人 Microsoft 个人帐户来使用你的 web API 来启用经过身份验证的用户。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>详情

以下是保护 Web API 需要了解的一些详细信息：

- 你的应用注册必须至少公开一个范围。 Web API 接受的令牌版本取决于登录受众。
- Web API 的代码配置必须验证调用 Web API 时使用的令牌。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-protected-web-api-app-registration.md)
