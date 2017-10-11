---
title: "Azure AD.NET 协议概述 |Microsoft 文档"
description: "如何使用 HTTP 消息来授予对 web 应用程序和你使用 Azure AD 的租户中的 web Api 的访问权限。"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## 向你的 AD 租户中注册你的应用程序
首先，你将需要向 Azure Active Directory (Azure AD) 租户中注册你的应用程序。 这将为你提供你的应用程序，应用程序 ID，以及使其能够接收令牌。

* 登录到[Azure 门户](https://portal.azure.com)。
* 通过单击页面右上角你的帐户选择你的 Azure AD 租户。
* 在左侧导航窗格中，单击**Azure Active Directory**。
* 单击**应用程序注册**，然后单击**添加**。
* 按照提示进行操作，并创建新的应用程序。 它不重要如果它是 web 应用程序或本教程中，本机应用程序，但如果你想要特定示例 web 应用程序或本机应用程序，请查看我们[快速入门](../articles/active-directory/develop/active-directory-developers-guide.md)。
  * 对于 Web 应用程序，提供**登录 URL**这是您的应用程序的基 URL 的用户可以登录例如`http://localhost:12345`。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 对于本机应用程序，提供**重定向 URI**，Azure AD 将使用它来返回令牌响应。 输入你的应用程序中，于特定的值。 例如`http://MyFirstAADApp`
* Azure AD 完成注册后，将分配你的应用程序的唯一客户端标识符，应用程序 id。 你将需要此值在下一步的部分中，因此，复制从应用程序页。
