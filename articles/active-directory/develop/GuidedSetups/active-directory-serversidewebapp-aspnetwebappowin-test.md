---
title: "Azure AD v2 ASP.NET Web 服务器获取已启动 – 测试 |Microsoft 文档"
description: "与传统 web 基于浏览器应用程序使用 OpenID Connect 标准在 ASP.NET 解决方案上实现 Microsoft 登录"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 00cb963e85111274c36c3a84489894811ad2dabd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="test-your-code"></a>测试你的代码

按`F5`若要在 Visual Studio 中运行你的项目。 浏览器将打开并将你定向*http://localhost: {port}* ，你将看到*使用 Microsoft 登录*按钮。 继续操作并单击它以登录。

当你准备好测试时，使用工作或学校 (Azure Active Directory) 或 （live.com、 outlook.com） 的个人帐户登录。 

![使用 Microsoft 浏览器窗口登录](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![使用 Microsoft 浏览器窗口登录](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>预期的结果
在登录后，用户重定向到您的网站这是你的应用程序注册信息在 Microsoft 应用程序注册门户中指定的 HTTPS URL 主页。 此页现在显示*Hello {User}*和更早版本创建的链接注销和一个链接来查看用户的声明 – 这是授权控制器的链接。

### <a name="see-users-claims"></a>查看用户的声明
选择超链接以查看用户的声明。 这将导致你的控制器和仅可供进行身份验证的用户的视图。

#### <a name="expected-results"></a>预期的结果
 你应看到一个包含已登录用户的基本属性表：

| 属性 | 值 | 描述|
|---|---|---|
| Name | {用户全名} | 用户的名字和姓氏
|Username | <span>user@domain.com</span>| 用于标识已登录的用户的用户名
| 使用者| {使用者}|一个字符串，唯一标识跨 web 的用户登录|
| 租户 ID| {Guid}| A *guid*来唯一地表示用户的 Azure Active Directory 组织。|

此外，你将看到包括身份验证请求中包含的所有用户声明的表。 有关 Id 令牌和说明中的所有声明的列表请参阅此[文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "Id 令牌中的声明列表")。


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>测试访问方法具有*[Authorize]*属性 （可选）
在此步骤中，你将测试访问作为匿名用户的身份验证控制器：<br/>
选择链接到注销的用户并完成注销过程。<br/>
现在在浏览器中，键入 http://localhost: {port} / 已经过身份验证来访问你为受保护的控制器`[Authorize]`属性

#### <a name="expected-results"></a>预期的结果
您应收到要求您进行身份验证以查看视图提示。

## <a name="additional-information"></a>其他信息

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>保护你的整个 web 站点
若要保护你的整个 web 站点，将添加`AuthorizeAttribute`到`GlobalFilters`中`Global.asax``Application_Start`方法：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **如何限制用户只有一个组织中登录你的应用程序**

> 默认情况下，个人帐户 （包括 outlook.com、 live.com 和其他），以及工作和学校帐户从任何公司或组织与 Azure Active Directory 集成可以登录到你的应用程序。 

> 如果要接受从只有一个 Azure Active Directory 组织登录你的应用程序，请替换`Tenant`中的参数*web.config*从`Common`组织 – 的租户名称的示例中， *contoso.onmicrosoft.com*。 之后，更改`ValidateIssuer`中的参数你*OWIN 启动类*到`true`。

> 若要允许用户从特定的组织的列表，将设置`ValidateIssuer`为 true，使用`ValidIssuers`参数来指定组织的列表。

> 另一个选项是实现自定义方法以验证使用 IssuerValidator 参数的颁发者。 有关详细信息`TokenValidationParameters`，请参阅[这](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters MSDN 文章")MSDN 文章。

