---
title: "Azure AD .NET 协议概述 | Microsoft Docs"
description: "如何使用 Azure AD，通过 HTTP 消息来授权访问租户中的 Web 应用程序和 Web API。"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
ms.openlocfilehash: 42ce8dfd30cda7d4085778954350550fd9fdf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
## <a name="register-your-application-with-your-ad-tenant"></a>将应用程序注册到 AD 租户
首先，需要将应用程序注册到 Azure Active Directory (Azure AD) 租户。 这会为应用程序分配一个应用程序 ID，并且使该应用程序可以接收令牌。

* 登录到 [Azure 门户](https://portal.azure.com)。
* 通过在页面右上角单击帐户选择 Azure AD 租户。
* 在左侧的导航窗格中，单击“Azure Active Directory”。
* 单击“应用注册”并单击“添加”。
* 根据提示创建新的应用程序。 本教程简要介绍了 Web 应用程序和本机应用程序的操作步骤，如果想要查看 Web 应用程序或本机应用程序的具体示例，请参阅[快速入门](../articles/active-directory/develop/active-directory-developers-guide.md)。
  * 对于 Web 应用程序，请在用户登录页面（如 `http://localhost:12345`）提供“登录 URL”，即应用的基 URL。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 对于本机应用程序，请提供“重定向 URI”，Azure AD 将用其返回令牌响应。 输入特定于应用程序的值，例如 `http://MyFirstAADApp`
* 完成注册后，Azure AD 将为应用程序分配一个唯一的客户端标识符，即应用程序 ID。 在后面的部分中会用到此值，因此，请从应用程序页复制此值。
