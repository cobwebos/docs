---
title: 向 ASP.NET Web 应用添加 Microsoft 登录功能 | Microsoft Docs
description: 了解如何使用 OpenID Connect 标准通过基于传统 Web 浏览器的应用程序根据 ASP.NET 解决方案添加 Microsoft 登录。
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 240b64bcaa242e39140f39faff31418f96b34193
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149917"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>快速入门：向 ASP.NET Web 应用添加 Microsoft 登录功能

[Microsoft 标识平台](v2-overview.md)由 Azure Active Directory (Azure AD) 开发人员平台演变而来。 开发人员可以通过它来生成应用程序，从而可以采用所有 Microsoft 标识登录，以及获取令牌来调用 Microsoft Graph 等 Microsoft API 或开发人员生成的 API。

借助 [Microsoft 身份验证库 (MSAL)](msal-overview.md)，开发人员能够从 Microsoft 标识平台终结点获取令牌，以访问受保护的 Web API。 Active Directory 身份验证库 (ADAL) 与适用于开发人员的 Azure AD (v1.0) 终结点集成，其中 MSAL 与 Microsoft 标识平台 (v2.0) 终结点集成。

## <a name="next-steps"></a>后续步骤

对于新的 Web 应用程序，我们建议你使用 Microsoft 标识平台 (v2.0) 和 MSAL 来获取令牌并访问受保护的 Web API。 请参阅[快速入门：向 ASP.NET Web 应用添加 Microsoft 登录功能](quickstart-v2-aspnet-webapp.md)以开始使用。
