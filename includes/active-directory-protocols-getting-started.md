---
title: Azure AD .NET 协议概述 | Microsoft Docs
description: 如何使用 Azure AD，通过 HTTP 消息来授权访问租户中的 Web 应用程序和 Web API。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522312"
---
## <a name="register-your-application-with-your-ad-tenant"></a>将应用程序注册到 AD 租户
首先，将应用程序注册到 Azure Active Directory （Azure AD）租户。 这会为应用程序分配一个应用程序 ID，并且使该应用程序可以接收令牌。

1. 登录到 [Azure 门户](https://portal.azure.com)。
   
1. 通过在页面右上角选择帐户，然后选择**交换机目录**导航，然后选择相应的租户，选择 Azure AD 租户。 
   - 如果你的帐户下只有一个 Azure AD 租户，或者你已选择相应的 Azure AD 租户，请跳过此步骤。
   
1. 在 Azure 门户中，搜索并选择 " **Azure Active Directory**"。
   
1. 在**Azure Active Directory**左侧菜单中，选择 "**应用注册**"，然后选择 "**新注册**"。
   
1. 根据提示创建新的应用程序。 对于本教程来说，它是 Web 应用程序还是公共客户端（移动和桌面）应用程序并不重要，但是如果你想要 Web 应用程序或公共客户端应用程序的特定示例，请查看我们的[快速入门](../articles/active-directory/develop/v1-overview.md)。
   
   - **名称**是应用程序名称，它向最终用户描述该应用程序。
   - 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
   - 提供**重定向 URI**。 对于 web 应用程序，这是你的应用程序的基本 URL，用户可以在其中登录。  例如，`http://localhost:12345`。 对于公共客户端（移动和桌面），Azure AD 使用它来返回令牌响应。 输入特定于应用程序的值。  例如，`http://MyFirstAADApp`。
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 完成注册后，Azure AD 将为应用程序分配一个唯一的客户端标识符（即应用程序 ID）。 在后面的部分中会用到此值，因此，请从应用程序页复制此值。
   
1. 若要在 Azure 门户中查找您的应用程序，请选择 "**应用注册**"，然后选择 "**查看所有应用程序**"。
