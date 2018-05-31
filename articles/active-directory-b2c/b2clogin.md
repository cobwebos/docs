---
title: 使用 b2clogin.com| Microsoft 文档
description: 了解如何使用 b2clogin.com 而不是 login.microsoftonline.com。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660415"
---
#<a name="using-b2clogincom"></a>使用 b2clogin.com

>[!IMPORTANT]
>此功能以公共预览版提供。 
>

现在，你可以选择使用带有 `<YourTenantName>.b2clogin.com` 的 Azure AD B2C 服务，而不是使用 `login.microsoftonline.com`。  这样做有诸多好处：
* 将不再与其他 Microsoft 产品共享相同的 Cookie 标头大小限制
* 可以在 URL 中删除对 Microsoft 的所有引用（可以将 `<YourTenantName>.onmicrosoft.com` 替换为你的租户 ID）

 为了利用 b2clogin.com，你需要设置以下某些内容：

1. 对于“立即运行终结点”，请确保使用的是 `<YourTenantName>.b2clogin.com` 而不是 `login.microsoftonline.com`。
2. 如果使用的是 MSAL，则需要设置 `validateauthority=false`。  这是因为令牌颁发者成为 `<YourTenantName>.b2clogin.com`。