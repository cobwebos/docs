---
title: 设置使用 Amazon 帐户的注册和登录
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory B2C 提供在应用程序中通过 Amazon 帐户注册与登录到客户的设置。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3149d45f50c53209e3be6be6688c9c2ce8fb0555
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900361"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Amazon 帐户注册与登录

## <a name="create-an-app-in-the-amazon-developer-console"></a>在 Amazon 开发人员控制台中创建应用

若要使用 Amazon 帐户作为 Azure Active Directory B2C （Azure AD B2C）中的联合标识提供者，需要在[Amazon 开发人员服务和技术](https://developer.amazon.com)中创建应用程序。 如果还没有 Amazon 帐户，可以在[https://www.amazon.com/](https://www.amazon.com/)注册。

> [!NOTE]  
> 在下面的**步骤 8**中使用以下 url， `your-tenant-name`并将替换为你的租户的名称。 输入租户名称时，请使用全部小写字母，即使租户在 Azure AD B2C 中使用大写字母定义。
> - 对于 "**允许的来源**"，输入`https://your-tenant-name.b2clogin.com` 
> - 对于 "**允许的返回 url**"，输入`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>将 Amazon 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录  。
1. 选择 Azure 门户左上角的“所有服务”****，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**"，然后选择**Amazon**。
1. 输入**名称**。 例如， *Amazon*。
1. 对于 "**客户端 id**"，请输入之前创建的 Amazon 应用程序的客户端 id。
1. 对于**客户端密码**，输入你记录的客户端密码。
1. 选择“保存”  。
