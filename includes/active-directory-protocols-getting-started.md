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
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173123"
---
## <a name="register-your-application-with-your-ad-tenant"></a>将应用程序注册到 AD 租户
首先，需要将应用程序注册到 Azure Active Directory (Azure AD) 租户。 这会为应用程序分配一个应用程序 ID，并且使该应用程序可以接收令牌。

* 登录到 [Azure 门户](https://portal.azure.com)。
* 通过以下方式选择 Azure AD 租户：在页面右上角单击你的帐户，单击“切换目录”  导航，然后选择合适的租户。 
  * 如果你的帐户下只有一个 Azure AD 租户，或者已选择了合适的 Azure AD 租户，请跳过此步骤。
* 在左侧的导航窗格中，单击“Azure Active Directory”。 
* 单击**应用注册**，然后单击**新注册**。
* 根据提示创建新的应用程序。 它并不重要如果它是 web 应用程序或本教程中，一个公共客户端 （移动和桌面） 应用程序，但如果想要的具体示例 web 应用程序或公共客户端应用程序，请查看我们[快速入门](../articles/active-directory/develop/v1-overview.md)。
  * **名称**是应用程序名称，它向最终用户描述该应用程序。
  * 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。  
  * 提供**重定向 URI**。 对于 Web 应用程序，这是您的应用程序的基 URL 的用户可以登录。  例如，`http://localhost:12345`。 对于公共客户端 （移动和桌面），Azure AD 使用它来返回令牌响应。 输入特定于应用程序的值。  例如，`http://MyFirstAADApp`。
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* 完成注册后，Azure AD 将为应用程序分配唯一的客户端标识符 (**应用程序 ID**)。 在后面的部分中会用到此值，因此，请从应用程序页复制此值。
* 若要在 Azure 门户中找到应用程序，请依次单击“应用注册”  、“查看所有应用程序”  。
