---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: bfdc89d9bc5d5a07c04e857c1a46e4b988c125ab
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943549"
---
## <a name="test-your-code"></a>测试代码

要在 Visual Studio 中测试应用程序，请按 F5 运行项目。 浏览器随即打开到 http://<span></span>localhost:{port} 位置，然后显示“使用 Microsoft 登录”按钮。 选择该按钮，启动登录过程。

准备好运行测试后，使用 Microsoft Azure Active Directory (Azure AD) 帐户（工作或学校帐户）或个人 Microsoft 帐户（live.com、outlook.com）进行登录<span></span><span></span>。

![Microsoft 登录](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![登录 Microsoft 帐户](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>查看应用程序结果
登录后，用户将重定向到网站主页。 该主页是 Microsoft 应用程序注册门户上应用程序注册信息中指定的 HTTPS URL。 主页包括欢迎消息“Hello \<User>”、一个注销链接和一个用于查看用户声明的链接。 用户声明链接浏览到你之前创建的“声明”控制器。

### <a name="browse-to-see-the-users-claims"></a>浏览查看用户声明
要查看用户声明，选择该链接以浏览到控制器视图（仅经过身份验证的用户可访问该视图）。

#### <a name="view-the-claims-results"></a>查看声明结果
浏览到控制器视图后，应当会显示包含用户基本属性的表格：

|属性 |值 |说明 |
|---|---|---|
|名称 |用户全名 | 用户的名字和姓氏。
|**用户名** |user<span>@domain.com</span> | 用于标识用户的用户名。
|**主题** |主题 |唯一标识 Web 上用户的字符串。|
|**租户 ID** |Guid | 唯一表示用户的 Azure AD 组织的 guid。|

此外，还应当显示包含身份验证请求中所有声明的表格。 有关详细信息，请参阅 [Azure AD ID 令牌中的声明列表](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>测试对具有 Authorize 属性的方法的访问（可选）
若要作为由 `Authorize` 属性保护的控制器的匿名用户来测试访问，请执行以下步骤：
1. 选择注销用户的链接并完成注销过程。
2. 在浏览器中，键入 http://<span></span>localhost:{port}/claims 来访问由 `Authorize` 属性保护的控制器。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>访问受保护控制器后的预期结果
系统会提示进行身份验证来使用受保护的控制器视图。

## <a name="advanced-options"></a>高级选项

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>保护整个网站
若要保护整个网站，请在 **Global.asax** 文件中向 `Application_Start` 方法中的 `GlobalFilters` 筛选器添加 `AuthorizeAttribute` 属性：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>限制谁可以登录到应用程序
默认情况下，当生成本指南创建的应用程序时，应用程序将接受使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及与 Azure Active Directory 集成的任何公司或组织的工作和学校帐户进行登录。 这是针对 SaaS 应用程序推荐使用的选项。

为了限制应用程序的用户登录访问权限，提供了以下多个选项：

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>选项 1：仅允许单个组织的 Active Directory 实例中的用户登录应用程序（单租户）

此选项是 LOB 应用程序的常见方案：如果希望应用程序仅接受属于特定 Azure Active Directory 实例的帐户（包括该实例的来宾帐户）进行登录，请执行以下操作：

1. 在 **web.config** 文件中，将 `Tenant` 参数的值从 `Common` 更改为组织的租户名称，例如 `contoso.onmicrosoft.com`。
2. 在 [OWIN Startup 类](#configure-the-authentication-pipeline)中，将 `ValidateIssuer` 参数设置为 `true`。

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>选项 2：仅允许特定列表中的组织中的用户访问应用程序

可以将登录访问权限仅限于允许组织列表中的 Azure AD 组织中的用户帐户：
1. 在 [OWIN Startup 类](#configure-the-authentication-pipeline)中，将 `ValidateIssuer` 参数设置为 `true`。
2. 将 `ValidIssuers` 参数的值设置为允许的组织的列表。

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>选项 3：使用自定义方法来验证颁发者
可通过 IssuerValidator 参数实现自定义方法来验证颁发者。 有关如何使用此参数的详细信息，请阅读 MSDN 上的 [TokenValidationParameters class](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)（TokenValidationParameters 类）。

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
