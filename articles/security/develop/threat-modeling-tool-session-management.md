---
title: 会话管理 - Microsoft 威胁建模工具 - Azure | Microsoft 文档
description: 针对威胁建模工具中暴露的威胁采取的缓解措施
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref
ms.openlocfilehash: 7ddc8c3016487ce56bc1a54d74aa94064cef24e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198864"
---
# <a name="security-frame-session-management"></a>安全框架：会话管理
| 产品/服务 | 文章 |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[使用 Azure AD 时借助 ADAL 方法实现适当的注销](#logout-adal)</li></ul> |
| IoT 设备 | <ul><li>[对生成的 SaS 令牌使用有限生存期](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[对生成的资源令牌使用最短的令牌生存期](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[使用 ADFS 时借助 WsFederation 方法实现适当的注销](#wsfederation-logout)</li></ul> |
| **标识服务器** | <ul><li>[使用标识服务器时实现适当的注销](#proper-logout)</li></ul> |
| **Web 应用程序** | <ul><li>[可通过 HTTPS 使用的应用程序必须使用安全 Cookie](#https-secure-cookies)</li><li>[所有基于 HTTP 的应用程序只应为 Cookie 定义指定 HTTP](#cookie-definition)</li><li>[缓解针对 ASP.NET 网页的跨站点请求伪造 (CSRF) 攻击](#csrf-asp)</li><li>[设置会话的非活动生存期](#inactivity-lifetime)</li><li>[从应用程序实现适当的注销](#proper-app-logout)</li></ul> |
| **Web API** | <ul><li>[缓解针对 ASP.NET Web API 的跨站点请求伪造 (CSRF) 攻击](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>使用 Azure AD 时借助 ADAL 方法实现适当的注销

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure AD | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 如果应用程序依赖于 Azure AD 颁发的访问令牌，注销事件处理程序应调用 |

### <a name="example"></a>示例
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>示例
此外，应该通过调用 Session.Abandon() 方法来销毁用户的会话。 以下方法演示了用户注销的安全实现：
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>对生成的 SaS 令牌使用有限生存期

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 设备 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 为了向 Azure IoT 中心进行身份验证而生成的 SaS 令牌应该附带有限的过期时间。 请尽量缩短 SaS 令牌的生存期，以便在令牌泄露的情况下，限制这些令牌的可重放时间。|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>对生成的资源令牌使用最短的令牌生存期

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure Document DB | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 将资源令牌的时间跨度减至所需的最小值。 资源令牌的有效时间跨度默认为 1 小时。|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>使用 ADFS 时借助 WsFederation 方法实现适当的注销

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | ADFS | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 如果应用程序依赖于 ADFS 颁发的 STS 令牌，注销事件处理程序应调用 WSFederationAuthenticationModule.FederatedSignOut() 方法来注销用户。 此外，应销毁当前会话，重置会话令牌值并将其设为 null。|

### <a name="example"></a>示例
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>使用标识服务器时实现适当的注销

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 标识服务器 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | [IdentityServer3 - 联合注销](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **步骤** | IdentityServer 支持与外部标识提供者联合。 当用户从上游标识提供者注销时，根据所使用的协议，当用户注销时，可能会收到通知。它允许 IdentityServer 通知其客户端，以便他们也可以注销用户。有关实现的详细信息，请参阅参考部分中的文档。|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>可通过 HTTPS 使用的应用程序必须使用安全 Cookie

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | EnvironmentType - OnPrem |
| **参考**              | [httpCookies 元素（ASP.NET 设置架构）](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx)、[HttpCookie.Secure 属性](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **步骤** | 通常只能从 Cookie 限定到的域访问这些 Cookie。 遗憾的是，“域”的定义不包括协议，因此，通过 HTTPS 创建的 Cookie 可通过 HTTP 访问。 “secure”特性可向浏览器指明，只能通过 HTTPS 使用 Cookie。 请确保通过 HTTPS 设置的所有 Cookie 使用 **secure** 特性。 可在 web.config 文件通过将 requireSSL 属性设置为 true，来强制实施此要求。 这是一种首选方法，因为它会强制所有当前和未来的 Cookie 使用 **secure** 属性，而无需对代码进一步进行更改。|

### <a name="example"></a>示例
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
即使使用 HTTP 访问应用程序，也会强制实施此设置。 如果使用 HTTP 访问应用程序，此设置会中断应用程序，因为 Cookie 设置为使用 secure 属性，而浏览器不会将这些 Cookie 发回到应用程序。

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | Web 窗体、MVC5 |
| **特性**              | EnvironmentType - OnPrem |
| **参考**              | N/A  |
| **步骤** | 如果 Web 应用程序是信赖方，IdP 是 ADFS 服务器，则可以通过在 web.config 的 `system.identityModel.services` 节中将 requireSSL 设置为 True，来配置 FedAuth 令牌的 secure 特性：|

### <a name="example"></a>示例
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>所有基于 HTTP 的应用程序只应为 Cookie 定义指定 HTTP

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | [Secure Cookie 特性](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **步骤** | 为了缓解跨站点脚本 (XSS) 攻击造成的信息泄露风险，Cookie 中引入了新特性 httpOnly，所有主流浏览器都支持此特性。 此属性指定不能通过脚本访问 Cookie。 Web 应用程序可以使用 HttpOnly Cookie 来减少通过脚本窃取 Cookie 中包含的敏感信息并将其发送到攻击者网站的可能性。 |

### <a name="example"></a>示例
所有使用 Cookie 的基于 HTTP 的应用程序应该通过在 web.config 中实现以下配置，在 Cookie 定义中指定 HttpOnly：
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | Web 窗体 |
| **特性**              | N/A  |
| **参考**              | [FormsAuthentication.RequireSSL 属性](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **步骤** | 可以使用配置元素的 requireSSL 特性，在 ASP.NET 应用程序的配置文件中设置 RequireSSL 属性值。 你可以在 ASP.NET 应用程序的 web.config 文件中指定传输层安全（TLS）（以前称为 SSL （安全套接字层））是否需要通过设置 requireSSL 属性将 forms 身份验证 cookie 返回到服务器。|

### <a name="example"></a>示例 
以下代码示例在 Web.config 文件中设置 requireSSL 特性。
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC5 |
| **特性**              | EnvironmentType - OnPrem |
| **参考**              | [Windows Identity Foundation (WIF) 配置 – 第 II 部分](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **步骤** | 要为 FedAuth Cookie 设置 httpOnly 特性，应将 hideFromCsript 特性值应设置为 True。 |

### <a name="example"></a>示例
以下配置显示正确的配置：
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>缓解针对 ASP.NET 网页的跨站点请求伪造 (CSRF) 攻击

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 跨站点请求伪造（CSRF 或 XSRF）攻击是指在在不同用户与网站建立的会话的安全上下文中执行操作。 其企图是在目标网站专门依赖使用会话 Cookie 对收到的请求进行身份验证的情况下修改或删除内容。 攻击者可能会利用此漏洞，获取不同用户的浏览器并在用户已登录到的有漏洞站点中使用某个命令加载 URL。 攻击者可以使用多种方式实现此目的，例如，托管一个不同的网站用于从有漏洞的服务器加载资源，或者诱骗用户点击某个链接。 如果服务器向客户端发送一个附加令牌，要求客户端在将来的所有请求中包含该令牌，并验证将来的所有请求是否包含与当前会话相关的令牌（例如，使用 ASP.NET AntiForgeryToken 或 ViewState），则可以避免这种攻击。 |

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC5、MVC6 |
| **特性**              | N/A  |
| **参考**              | [ASP.NET MVC 和网页中的 XSRF/CSRF 预防](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **步骤** | 反 CSRF 和 ASP.NET MVC 窗体 - 例如，在视图中使用 `AntiForgeryToken` 帮助器方法；将 `Html.AntiForgeryToken()` 放入窗体。|

### <a name="example"></a>示例
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>示例
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>示例
同时，Html.AntiForgeryToken() 将为访问者提供一个名为 __RequestVerificationToken 的 Cookie，其值与上面所示的随机隐藏值相同。 接下来，要验证传入的窗体发布内容，请将 [ValidateAntiForgeryToken] 筛选器添加到目标操作方法。 例如：
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
可以检查以下各项的授权筛选器：
* 传入的请求是否包含名为 __RequestVerificationToken 的 Cookie
* 传入的请求是否包含名为 __RequestVerificationToken 的 `Request.Form` 条目
* 这些 Cookie 是否与 `Request.Form` 值匹配。假设一切正常，则请求可以顺利传输。 否则，授权会失败并出现消息“未提供所需的防伪令牌或者该令牌无效”。 

### <a name="example"></a>示例
反 CSRF 和 AJAX：窗体令牌可能对 AJAX 请求造成问题，因为 AJAX 请求可以发送 JSON 数据，但不能发送 HTML 窗体数据。 一种解决方法是在自定义 HTTP 标头中发送令牌。 以下代码使用 Razor 语法生成令牌，然后将令牌添加到 AJAX 请求。 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>示例
处理请求时，请从请求标头中提取令牌。 然后调用 AntiForgery.Validate 方法来验证令牌。 如果令牌无效，Validate 方法将引发异常。
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | Web 窗体 |
| **特性**              | N/A  |
| **参考**              | [利用 ASP.NET 内置功能来防范 Web 攻击](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **步骤** | 可以通过将 ViewStateUserKey 设置为某个随机字符串（该字符串因用户而异，可根据用户 ID 设置，最好是根据会话 ID 设置），来缓解基于 Web 窗体的应用程序中的 CSRF 攻击。 出于技术和社会方面的多种原因，根据会话 ID 设置此属性要好得多，因为会话 ID 不可预测、会超时，并且根据不同的用户而异。|

### <a name="example"></a>示例
需要在所有页面中添加以下代码：
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>设置会话的非活动生存期

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | [HttpSessionState.Timeout 属性](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **步骤** | 会话超时表示用户在某个时间间隔（由 web 服务器定义）内未在网站上执行任何操作时出现的事件。 服务器端发生该事件时，会将用户会话的状态更改为“invalid”（即，“不再使用”），并指示 Web 服务器销毁该会话（删除其中包含的所有数据）。 以下代码示例在 Web.config 文件中将超时会话特性设置为 15 分钟。|

### <a name="example"></a>示例
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>在 Azure SQL 中启用威胁检测

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | Web 窗体 |
| **特性**              | N/A  |
| **参考**              | [Authentication 的 Forms 元素（ASP.NET 设置架构）](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **步骤** | 将窗体身份验证票证 Cookie 超时设置为 15 分钟|

### <a name="example"></a>示例
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | Web 窗体、MVC5 |
| **特性**              | EnvironmentType - OnPrem |
| **参考**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **步骤** | 当 Web 应用程序是信赖方且 ADFS 是 STS 时，可以通过 web.config 中的以下配置设置身份验证 cookie FedAuth 令牌的生存期：|

### <a name="example"></a>示例
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>示例
此外，应该通过在 ADFS 服务器上执行以下 powershell 命令，将 ADFS 颁发的 SAML 声明令牌的生存期设置为 15 分钟：
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>通过应用程序实现适当的注销

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 当用户按下注销按钮时，从应用程序执行适当的注销。 注销后，应用程序应销毁用户的会话，重置会话 Cookie 值并将其设置为 null，同时重置身份验证 Cookie 值并将其设置为 null。 此外，如果多个会话绑定到单个用户标识，在超时或注销时，必须在服务器端统一终止这些会话。 最后，确保可在每个页面上使用注销功能。 |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>缓解针对 ASP.NET Web API 的跨站点请求伪造 (CSRF) 攻击

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **特性**              | N/A  |
| **参考**              | N/A  |
| **步骤** | 跨站点请求伪造（CSRF 或 XSRF）攻击是指在在不同用户与网站建立的会话的安全上下文中执行操作。 其企图是在目标网站专门依赖使用会话 Cookie 对收到的请求进行身份验证的情况下修改或删除内容。 攻击者可能会利用此漏洞，获取不同用户的浏览器并在用户已登录到的有漏洞站点中使用某个命令加载 URL。 攻击者可以使用多种方式实现此目的，例如，托管一个不同的网站用于从有漏洞的服务器加载资源，或者诱骗用户点击某个链接。 如果服务器向客户端发送一个附加令牌，要求客户端在将来的所有请求中包含该令牌，并验证将来的所有请求是否包含与当前会话相关的令牌（例如，使用 ASP.NET AntiForgeryToken 或 ViewState），则可以避免这种攻击。 |

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC5、MVC6 |
| **特性**              | N/A  |
| **参考**              | [防止 ASP.NET Web API 中的跨站点请求伪造 (CSRF) 攻击](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **步骤** | 反 CSRF 和 AJAX：窗体令牌可能对 AJAX 请求造成问题，因为 AJAX 请求可以发送 JSON 数据，但不能发送 HTML 窗体数据。 一种解决方法是在自定义 HTTP 标头中发送令牌。 以下代码使用 Razor 语法生成令牌，然后将令牌添加到 AJAX 请求。 |

### <a name="example"></a>示例
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>示例
处理请求时，请从请求标头中提取令牌。 然后调用 AntiForgery.Validate 方法来验证令牌。 如果令牌无效，Validate 方法将引发异常。
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>示例
反 CSRF 和 ASP.NET MVC 窗体 - 例如，在视图中使用 AntiForgeryToken 帮助器方法；将 Html.AntiForgeryToken() 放入窗体。
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>示例
上述示例输出如下所示的信息：
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>示例
同时，Html.AntiForgeryToken() 将为访问者提供一个名为 __RequestVerificationToken 的 Cookie，其值与上面所示的随机隐藏值相同。 接下来，要验证传入的窗体发布内容，请将 [ValidateAntiForgeryToken] 筛选器添加到目标操作方法。 例如：
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
可以检查以下各项的授权筛选器：
* 传入的请求是否包含名为 __RequestVerificationToken 的 Cookie
* 传入的请求是否包含名为 __RequestVerificationToken 的 `Request.Form` 条目
* 这些 Cookie 是否与 `Request.Form` 值匹配。假设一切正常，则请求可以顺利传输。 否则，授权会失败并出现消息“未提供所需的防伪令牌或者该令牌无效”。

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC5、MVC6 |
| **特性**              | 标识提供者 - ADFS，标识提供者 - Azure AD |
| **参考**              | [在 ASP.NET Web API 2.2 中使用单个帐户和本地登录名保护 Web API](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **步骤** | 如果 Web API 是使用 OAuth 2.0 保护的，则它需要在授权请求标头中包含一个持有者令牌，仅当该令牌有效时，才向请求授予访问权限。 与基于 Cookie 的身份验证不同，浏览器不会将持有者令牌附加到请求。 请求方客户端需要在请求标头中显式附加持有者令牌。 因此，对于使用 OAuth 2.0 保护的 ASP.NET Web API，持有者令牌被视为一种防范 CSRF 攻击的措施。 请注意，如果应用程序的 MVC 部分使用窗体身份验证（即，使用 Cookie），则 MVC Web 应用必须使用防伪令牌。 |

### <a name="example"></a>示例
必须告知 Web API 只能依赖于持有者令牌，而不能依赖于 Cookie。 此操作可通过 `WebApiConfig.Register` 方法中的以下配置完成：

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

SuppressDefaultHostAuthentication 方法通过 IIS 或 OWIN 中间件告知 Web API 忽略在请求到达 Web API 管道前发生的任何身份验证。 如此，便可以限制 Web API 仅使用持有者令牌进行身份验证。
