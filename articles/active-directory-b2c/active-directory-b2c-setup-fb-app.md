---
title: 使用 Facebook 帐户设置注册与登录 - Azure Active Directory B2C | Microsoft Docs
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Facebook 帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6b90ba89f17b42f4d92c666c3f539fcad3e3227c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733523"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Facebook 帐户注册与登录

## <a name="create-a-facebook-application"></a>创建 Facebook 应用程序

若要将 Facebook 帐户用作 Azure Active Directory (Azure AD) B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 Facebook 帐户，可以在 [https://www.facebook.com/](https://www.facebook.com/) 获取。

1. 使用 Facebook 帐户凭据登录 [Facebook 开发人员](https://developers.facebook.com/)。
2. 如果以前没有登录过，需要注册为 Facebook 开发人员。 若要执行此操作，请选择**开始**在页面的右上角，接受 Facebook 的策略，并完成注册步骤。
3. 选择**我的应用**，然后**添加新的应用程序**。
4. 输入“显示名称”  和有效的“联系人电子邮件”  。
5. 单击“创建应用程序 ID”  。 这会要求接受 Facebook 平台策略并完成在线安全检查。
6. 选择“设置”   > “基本”  。
7. 选择“类别”  ，例如 `Business and Pages`。 此值是 Facebook 必需的，但不用于 Azure AD B2C。
8. 在页面底部，选择“添加平台”  ，然后选择“网站”  。
9. 在“站点 URL”  中，输入 `https://your-tenant-name.b2clogin.com/`，将 `your-tenant-name` 替换为你的租户名称。 为“隐私策略 URL”  输入一个 URL，例如 `http://www.contoso.com`。 策略 URL 是继续提供应用程序的隐私信息的页面。
10. 选择“保存更改”。 
11. 在页面的顶部，复制“应用 ID”  的值。
12. 单击“显示”  ，并复制“应用程序密码”  的值。 使用这两个值将 Facebook 配置为租户中的标识提供者。 “应用程序密码”是一个非常重要的安全凭据  。
13. 下一步选择加号**产品**，然后选择**设置**下**Facebook 登录**。
14. 下**Facebook 登录名**，选择**设置**。
15. 在“有效的 OAuth 重定向 URL”  中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 单击页面底部的“保存更改”  。
16. 若要使 Facebook 应用程序可在 Azure AD B2C 中使用，请单击页面右上角的状态选择器，将其设置为“打开”以公开该应用程序，然后单击“确认”   。  此时，状态应从“开发”变为“实时”   。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>将 Facebook 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。
3. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
4. 选择“标识提供者”  ，然后选择“添加”  。
5. 输入“名称”  。 例如，输入“Facebook”  。
6. 选择“标识提供者类型”  ，选择“Facebook”  ，并单击“确定”  。
7. 选择“设置此标识提供者”  ，然后输入你之前记录为“客户端 ID”  的应用 ID，并输入你记录为之前创建的 Facebook 应用程序的“客户端机密”  的应用机密。
8. 单击“确定”  ，并单击“创建”  以保存 Facebook 配置。
