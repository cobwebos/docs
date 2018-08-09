---
title: 开始在 Visual Studio WebApi 项目中使用 Azure AD
description: 通过 Visual Studio 连接服务连接到或创建 Azure AD 之后，如何在 WebApi 项目中开始使用 Azure Active Directory
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 6ddc1e8f7520135092eefbcbf2b4a2b3f1026e66
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494707"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Azure Active Directory 入门（WebApi 项目）

> [!div class="op_single_selector"]
> - [入门](vs-active-directory-webapi-getting-started.md)
> - [发生了什么情况](vs-active-directory-webapi-what-happened.md)

本文提供通过 Visual Studio 的“项目”>“连接的服务”命令将 Active Directory 添加到 ASP.NET WebAPI 项目后的其他指南。 如果尚未将该服务添加到你的项目，可以随时执行该操作。

请参阅[我的 WebAPI 项目发生了什么情况？](vs-active-directory-webapi-what-happened.md)，了解添加连接服务时对项目所做的更改。

## <a name="requiring-authentication-to-access-controllers"></a>访问控制器需要身份验证

项目中的所有控制器均带有 `[Authorize]` 属性。 此属性要求用户先进行身份验证，才能访问由这些控制器定义的 API。 若要允许匿名访问控制器，请从控制器删除此属性。 如果想要更详细地设置这些权限，请将该属性应用到需要身份验证的每个方法，而不是将它应用到控制器类。

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 的身份验证方案](authentication-scenarios.md)
- [向 ASP.NET Web 应用添加 Microsoft 登录功能](quickstart-v1-aspnet-webapp.md)
