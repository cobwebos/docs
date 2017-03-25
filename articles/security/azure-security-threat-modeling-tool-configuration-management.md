---
title: "配置管理 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
description: "针对威胁建模工具中暴露的威胁采取的缓解措施"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 66465e74298c08d64b37ab0ede86f3d187068555
ms.lasthandoff: 03/10/2017


---

# <a name="security-frame-configuration-management--mitigations"></a>安全框架：配置管理 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| Web 应用程序 | <ul><li>[实施内容安全策略 (CSP) 并禁用内联 JavaScript](#csp-js)</li><li>[启用浏览器的 XSS 筛选器](#xss-filter)</li><li>[ASP.NET 应用程序在部署之前必须禁用跟踪和调试](#trace-deploy)</li><li>[仅从受信任源访问第三方 JavaScript](#js-trusted)</li><li>[确保在经过身份验证的 ASP.NET 页面中整合 UI 伪装或点击劫持防御机制](#ui-defenses)</li><li>[确保已在 ASP.NET Web 应用程序中启用 CORS 的情况下只允许受信任的来源](#cors-aspnet)</li><li>[在 ASP.NET 页面中启用 ValidateRequest 特性](#validate-aspnet)</li><li>[使用本地托管的最新版本的 JavaScript 库](#local-js)</li><li>[禁用自动 MIME 探查](#mime-sniff)</li><li>[在 Windows Azure 网站中删除标准服务器标头避免留下指纹](#standard-finger)</li></ul> |
| 数据库 | <ul><li>[为数据库引擎访问配置 Windows 防火墙](#firewall-db)</li></ul> |
| Web API | <ul><li>[确保已在 ASP.NET Web API 中启用 CORS 的情况下只允许受信任的来源](#cors-api)</li><li>[加密 Web API 配置文件中包含敏感数据的部分](#config-sensitive)</li></ul> |
| IoT 设备 | <ul><li>[确保使用强凭据保护所有管理界面](#admin-strong)</li><li>[确保未知的代码无法在设备上执行](#unknown-exe)</li><li>[使用 Bitlocker 加密 IoT 设备的 OS 和其他分区](#partition-iot)</li><li>[确保只在设备上启用少量的服务/功能](#min-enable)</li></ul> |
| IoT 现场网关 | <ul><li>[使用 Bitlocker 加密 IoT 现场网关的 OS 和其他分区](#field-bitlocker)</li><li>[确保在安装期间更改现场网关的默认登录凭据](#default-change)</li></ul> |
| IoT 云网关 | <ul><li>[确保云网关实施相应的过程来保持已连接设备固件的最新状态](#cloud-firmware)</li></ul> |
| 计算机信任边界 | <ul><li>[确保根据组织的策略在设备上配置终结点安全控制](#controls-policies)</li></ul> |
| Azure 存储空间 | <ul><li>[确保对 Azure 存储访问密钥进行安全管理](#secure-keys)</li><li>[确保已在 Azure 存储中启用 CORS 的情况下只允许受信任的来源](#cors-storage)</li></ul> |
| WCF | <ul><li>[启用 WCF 的服务限制功能](#throttling)</li><li>[WCF - 通过元数据透露信息](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>实施内容安全策略 (CSP) 并禁用内联 JavaScript

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [内容安全策略简介](http://www.html5rocks.com/en/tutorials/security/content-security-policy/)、[内容安全策略参考](http://content-security-policy.com/)、[安全功能](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/)、[内容安全策略简介](https://docs.webplatform.org/wiki/tutorials/content-security-policy)、[是否可以使用 CSP？](http://caniuse.com/#feat=contentsecuritypolicy) |
| 步骤 | <p>内容安全策略 (CSP) 是一种深度防护安全机制，也是一项 W3C 标准，可让 Web 应用程序所有者控制其站点中嵌入的内容。 CSP 以 HTTP 响应标头的形式添加在 Web 服务器上，由浏览器在客户端实施。 它是基于允许列表的策略 - 网站可以声明一组受信任的域，而通过这些域可以加载 JavaScript 等活动内容。</p><p>CSP 提供以下安全优势：</p><ul><li>**XSS 防护：**如果页面容易受到 XSS 攻击，攻击者可通过两种方式利用这种漏洞：<ul><li>注入 `<script>malicious code</script>`。 由于 CSP 采用 Base Restriction-1，因此这种攻击不起作用</li><li>注入 `<script src=”http://attacker.com/maliciousCode.js”/>`。 由于攻击者控制的域不会出现在 CSP 的域允许列表中，因此这种攻击不起作用</li></ul></li><li>**控制数据渗透：**如果网页中的任何恶意内容尝试连接到外部网站并窃取数据，CSP 将中止该连接。 这是因为，目标域不会出现在 CSP 的允许列表中</li><li>**防范点击劫持：**点击劫持是一种攻击技法，攻击者布设一个正规的网站，诱迫用户点击其中的 UI 元素。 目前，针对点击劫持的防范措施是通过配置 响应标头 X-Frame-Options 实现的。 并非所有浏览器都支持此标头，从趋势来看，CSP 将是防范点击劫持的标准方式。</li><li>**实时攻击报告：**如果启用 CSP 的网站上发生注入攻击，浏览器将自动向 Web 服务器上配置的终结点触发通知。 这样，CSP 便充当了实时警告系统。</li></ul> |

### <a name="example"></a>示例
示例策略： 
```C#
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
此策略只允许从 Web 应用程序的服务器和 google 分析服务器加载脚本。 从其他任何站点加载的脚本将被拒绝。 在网站上启用 CSP 后，以下功能将自动禁用以缓解 XSS 攻击。 

### <a name="example"></a>示例
内联脚本不会执行。 下面是内联脚本的示例 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>示例
不会将字符串作为代码评估。 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>启用浏览器的 XSS 筛选器

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [XSS 保护筛选器](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| 步骤 | <p>X-XSS-Protection 响应标头配置控制浏览器的跨站点脚本筛选器。 此响应标头可使用以下值：</p><ul><li>`0:`：禁用筛选器</li><li>`1: Filter enabled`：如果检测到跨站点脚本攻击，浏览器将净化页面以阻止攻击</li><li>`1: mode=block : Filter enabled`。 检测到 XSS 攻击时，浏览器将阻止呈现页面，而不是净化页面</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`。 浏览器将净化页面并报告违规。</li></ul><p>这是一个 Chromium 函数，它利用 CSP 违规报告将详细信息发送到所选的 URI。 最后 2 个选项被视为安全值。</p>|

## <a id="trace-deploy"></a>ASP.NET 应用程序在部署之前必须禁用跟踪和调试

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [ASP.NET 调试概述](http://msdn2.microsoft.com/library/ms227556.aspx)、[ASP.NET 跟踪概述](http://msdn2.microsoft.com/library/bb386420.aspx)、[如何：为 ASP.NET 应用程序启用跟踪](http://msdn2.microsoft.com/library/0x5wc973.aspx)、[如何：为 ASP.NET 应用程序启用调试](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| 步骤 | 为页面启用跟踪后，请求该页面的每个浏览器也会获取包含有关内部服务器状态和工作流的数据的跟踪信息。 该信息可能是安全敏感信息。 为页面启用调试后，服务器上发生的错误会导致向浏览器提供完整的堆栈跟踪数据。 该数据可能会透露有关服务器工作流的安全敏感信息。 |

## <a id="js-trusted"></a>仅从受信任源访问第三方 JavaScript

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | 只应从受信任源引用第三方 JavaScript。 引用终结点应始终在 SSL 连接中。 |

## <a id="ui-defenses"></a>确保在经过身份验证的 ASP.NET 页面中整合 UI 伪装或点击劫持防御机制

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [OWASP 点击劫持防御速查表](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet)、[IE 内部 - 使用 X-Frame-Options 阻击点击劫持](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| 步骤 | <p>点击劫持也称“UI 伪装攻击”，是指攻击者使用多个透明或不透明的层，在用户想要点击顶层页面时，诱使用户点击另一个页面上的按钮或链接。</p><p>这种分层是通过编写使用 iframe 的恶意页面，加载受害者的页面来实现的。 因此，攻击者正是“劫持”了用户原本要在其页面上的点击，将他们路由到很有可能是由其他应用程序和/或域拥有的另一个页面。 若要阻止点击劫持攻击，请设置适当的 X-Frame-Options HTTP 响应标头，指示浏览器不要允许来自其他域的框架设计</p>|

### <a name="example"></a>示例
可以通过 IIS web.config 设置 X-FRAME-OPTIONS 标头。 永远不应设计框架的站点 Web.config 代码片段： 
```C#
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>示例
只应由同一域中的页面设计框架的站点 Web.config 代码： 
```C#
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>确保已在 ASP.NET Web 应用程序中启用 CORS 的情况下只允许受信任的来源

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | Web 窗体、MVC5 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | <p>浏览器安全性将阻止网页向另一个域发出 AJAX 请求。 这种限制称为同域策略，可阻止恶意站点读取另一个站点中的敏感数据。 但是，有时可能需要安全公开可由其他站点使用的 API。 跨域资源共享 (CORS) 是一项 W3C 标准，可让服务器放宽同域策略。 使用 CORS，服务器可以显式允许某些跨域请求，同时拒绝另一些跨域请求。</p><p>与 JSONP 等早期技术相比，CORS 更安全且更灵活。 从核心上讲，启用 CORS 就相当于在 Web 应用程序中添加了一些 HTTP 响应标头 (Access-Control-*)，这可以通过多种方式来实现。</p>|

### <a name="example"></a>示例
如果可以访问 Web.config，则可通过以下代码添加 CORS： 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="http://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>示例
如果无法访问 Web.config，则可通过添加以下 CSharp 代码来配置 CORS： 
```C#
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

请注意，务必将“Access-Control-Allow-Origin”特性中的来源列表设置为一组有限的受信任来源。 不当地配置此列表（例如，将值设置为“*”）将会使得恶意站点能够毫无限制地向 Web 应用程序触发跨域请求，从而使应用程序易于遭受 CSRF 攻击。 

## <a id="validate-aspnet"></a>在 ASP.NET 页面中启用 ValidateRequest 特性

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | Web 窗体、MVC5 |
| 属性              | 不适用  |
| 参考              | [请求验证 - 阻止脚本攻击](http://www.asp.net/whitepapers/request-validation) |
| 步骤 | <p>请求验证是 ASP.NET 版本 1.1 中的一项功能，可阻止服务器接受包含未编码 HTML 的内容。 此功能旨在帮助阻止某些脚本注入攻击，避免在不知情的情况下将客户端脚本代码或 HTML提交到服务器，然后将其存储并提供给其他用户。 尽管如此，我们仍旧强烈建议验证所有输入数据和 HTML，并在适当的情况下将其编码。</p><p>请求验证的执行方式是将所有输入数据与潜在危险值的列表进行比较。 如果出现匹配项，ASP.NET 将引发 `HttpRequestValidationException`。 请求验证功能默认已启用。</p>|

### <a name="example"></a>示例
但是，可在页面级别禁用此功能： 
```XML
<%@ Page validateRequest="false" %> 
```
或者在应用程序级别 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
请注意，MVC6 管道不支持且不包括请求验证功能。 

## <a id="local-js"></a>使用本地托管的最新版本的 JavaScript 库

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | <p>使用 JQuery 等标准 JavaScript 库的开发人员必须使用不包含已知安全漏洞的、批准的通用 JavaScript 库版本。 适当的做法是使用最新的库版本，因为它们包含其旧版本中已知漏洞的安全修补程序。</p><p>如果由于兼容性原因而无法使用最新版本，应使用以下最低版本。</p><p>可接受的最低版本：</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web 窗体和 Ajax**<ul><li>ASP.NET Web 窗体和 Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>切勿从外部站点（例如公共 CDN）加载任何 JavaScript 库</p>|

## <a id="mime-sniff"></a>禁用自动 MIME 探查

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [IE8 安全性第五部分：全面保护](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)、[MIME 类型](http://en.wikipedia.org/wiki/Mime_type) |
| 步骤 | X-Content-Type-Options 标头是一个 HTTP 标头，可让开发人员指定不应该对其内容使用 MIME 探查。 此标头旨在缓解 MIME 探查攻击。 对于可能包含用户可控内容的每个页面，必须使用 HTTP 标头 X-Content-Type-Options:nosniff。 若要针对应用程序中的所有页面全局启用所需的标头，可执行以下操作之一|

### <a name="example"></a>示例
如果应用程序由 Internet Information Services (IIS) 7 及更高版本托管，请在 web.config 文件中添加该标头。 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>示例
通过全局 Application\_BeginRequest 添加该标头 
```C#
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>示例
实现自定义的 HTTP 模块 
```C#
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>示例
对于特定的页面，只能通过将所需的标头添加到单个响应来启用该标头： 

```C#
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>在 Windows Azure 网站中删除标准服务器标头避免留下指纹

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web 应用程序 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | EnvironmentType - Azure |
| 参考              | [在 Windows Azure 网站中删除标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| 步骤 | Server、X-Powered-By、X-AspNet-Version 等标头会透露有关服务器和底层技术的信息。 建议取消这些标头，从而防止留下应用程序的指纹 |

## <a id="firewall-db"></a>为数据库引擎访问配置 Windows 防火墙

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | 数据库 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | SQL Azure、OnPrem |
| 属性              | 不适用，SQL 版本 - V12 |
| 参考              | [如何配置 Azure SQL 数据库防火墙](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/)、[为数据库引擎访问配置 Windows 防火墙](https://msdn.microsoft.com/library/ms175043) |
| 步骤 | 防火墙系统可帮助阻止对计算机资源进行未经授权的访问。 若要通过防火墙访问 SQL Server 数据库引擎的实例，必须将运行 SQL Server 的计算机上的防火墙配置为允许这种访问 |

## <a id="cors-api"></a>确保已在 ASP.NET Web API 中启用 CORS 的情况下只允许受信任的来源

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web API | 
| SDL 阶段               | 构建 |  
| 适用的技术 | MVC 5 |
| 属性              | 不适用  |
| 参考              | [在 ASP.NET Web API 2 中启用跨域请求](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)、[ASP.NET Web API - ASP.NET Web API 2 中的 CORS 支持](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| 步骤 | <p>浏览器安全性将阻止网页向另一个域发出 AJAX 请求。 这种限制称为同域策略，可阻止恶意站点读取另一个站点中的敏感数据。 但是，有时可能需要安全公开可由其他站点使用的 API。 跨域资源共享 (CORS) 是一项 W3C 标准，可让服务器放宽同域策略。</p><p>使用 CORS，服务器可以显式允许某些跨域请求，同时拒绝另一些跨域请求。 与 JSONP 等早期技术相比，CORS 更安全且更灵活。</p>|

### <a name="example"></a>示例
在 App_Start/WebApiConfig.cs 中，将以下代码添加到 WebApiConfig.Register 方法 
```C#
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>示例
可按如下所示，将 EnableCors 特性应用到控制器中的操作方法： 

```C#
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

请注意，务必将 EnableCors 特性中的来源列表设置为一组有限的受信任来源。 不当地配置此列表（例如，将值设置为“*”）将会使得恶意站点能够毫无限制地向 API 触发跨域请求，从而使 API 易于遭受 CSRF 攻击。 可在控制器级别修饰 EnableCors。 

### <a name="example"></a>示例
若要对类中的特定方法禁用 CORS，可按如下所示使用 DisableCors 特性： 
```C#
[EnableCors("http://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web API | 
| SDL 阶段               | 构建 |  
| 适用的技术 | MVC 6 |
| 属性              | 不适用  |
| 参考              | [在 ASP.NET Core 1.0 中启用跨域请求 (CORS)](https://docs.asp.net/en/latest/security/cors.html) |
| 步骤 | <p>在 ASP.NET Core 1.0 中，可以使用中间件或 MVC 启用 CORS。 使用 MVC 启用 CORS 时，将使用相同的 CORS 服务，但使用 CORS 中间件启用 CORS 时则不是这样。</p>|

**方式&1;** 使用中间件启用 CORS：若要为整个应用程序启用 CORS，请使用 UseCors 扩展方法将 CORS 中间件添加到请求管道。 使用 CorsPolicyBuilder 类添加 CORS 中间件时，可以指定跨域策略。 可通过两种方式实现此目的：

### <a name="example"></a>示例
第一种方式是使用 lambda 调用 UseCors。 lambda 使用一个 CorsPolicyBuilder 对象： 
```C#
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("http://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>示例
第二种方式是定义一个或多个命名的 CORS 策略，然后在运行时按名称选择策略。 
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("http://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**方式&2;** 在 MVC 中启用 CORS：开发人员也可以使用 MVC 按操作、按控制器，或者针对所有控制器全局应用特定的 CORS。

### <a name="example"></a>示例
按操作：若要为特定的操作指定 CORS 策略，请将 [EnableCors] 特性添加到该操作。 指定策略名称。 
```C#
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>示例
按控制器： 
```C#
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>示例
全局： 
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
请注意，务必将 EnableCors 特性中的来源列表设置为一组有限的受信任来源。 不当地配置此列表（例如，将值设置为“*”）将会使得恶意站点能够毫无限制地向 API 触发跨域请求，从而使 API 易于遭受 CSRF 攻击。 

### <a name="example"></a>示例
若要为控制器或操作禁用 CORS，请使用 [DisableCors] 特性。 
```C#
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>加密 Web API 配置文件中包含敏感数据的部分

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Web API | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [如何：使用 DPAPI 加密 ASP.NET 2.0 中的配置部分](https://msdn.microsoft.com/library/ff647398.aspx)、[指定受保护的配置提供程序](https://msdn.microsoft.com/library/68ze1hb2.aspx)、[使用 Azure Key Vault 保护应用程序机密](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| 步骤 | Web.config、appsettings.json 等配置文件通常用于保存敏感信息，包括用户名、密码、数据库连接字符串和加密密钥。 如果不保护此类信息，攻击者或恶意用户可能会利用应用程序的漏洞来获取敏感信息，例如帐户用户名和密码、数据库名称和服务器名称。 请根据部署类型 (azure/on-prem)，使用 DPAPI 或 Azure Key Vault 等服务来加密配置文件的敏感部分。 |

## <a id="admin-strong"></a>确保使用强凭据保护所有管理界面

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 设备 | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | 应使用强凭据保护设备或现场网关公开的任何管理接口。 此外，应使用强凭据保护 WiFi、SSH、文件共享、FTP 等其他任何公开的接口。 不应使用默认的弱密码。 |

## <a id="unknown-exe"></a>确保未知的代码无法在设备上执行

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 设备 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [在 Windows 10 IoT Core 上启用安全启动和 BitLocker 设备加密](https://developer.microsoft.com/windows/iot/win10/sb_bl) |
| 步骤 | UEFI 安全启动会将系统限制为只允许执行指定的颁发机构签名的二进制文件。 此功能可防止在平台上执行未知的代码，潜在地削弱这种代码的安全风险。 启用 UEFI 安全启动，并限制受信任的、可为代码签名的证书颁发机构列表。 使用受信任的颁发机构之一为设备上部署的所有代码签名。 |

## <a id="partition-iot"></a>使用 Bitlocker 加密 IoT 设备的 OS 和其他分区

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 设备 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | Windows 10 IoT Core 实现轻量版本的 BitLocker 设备加密，该版本在很大程度上依赖于平台上的 TPM，包括需要在 UEFI 中安装必要的 preOS 协议用于执行必要的度量。 这些 preOS 度量可确保 OS 今后拥有一份有关 OS 启动方式的明确记录。使用 Bitlocker 加密 OS 分区，如果其他任何分区中存储了任何敏感数据，也应该加密这些分区。 |

## <a id="min-enable"></a>确保只在设备上启用少量的服务/功能

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 设备 | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | 不要在 OS 中启用或者请关闭运行解决方案所不需要的任何功能或服务。 例如，如果设备不需要部署 UI，请在无外设模式下安装 Windows IoT Core。 |

## <a id="field-bitlocker"></a>使用 Bitlocker 加密 IoT 现场网关的 OS 和其他分区

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 现场网关 | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | Windows 10 IoT Core 实现轻量版本的 BitLocker 设备加密，该版本在很大程度上依赖于平台上的 TPM，包括需要在 UEFI 中安装必要的 preOS 协议用于执行必要的度量。 这些 preOS 度量可确保 OS 今后拥有一份有关 OS 启动方式的明确记录。使用 Bitlocker 加密 OS 分区，如果其他任何分区中存储了任何敏感数据，也应该加密这些分区。 |

## <a id="default-change"></a>确保在安装期间更改现场网关的默认登录凭据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 现场网关 | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | 确保在安装期间更改现场网关的默认登录凭据 |

## <a id="cloud-firmware"></a>确保云网关实施相应的过程来保持已连接设备固件的最新状态

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | IoT 云网关 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 网关选项 - Azure IoT 中心 |
| 参考              | [IoT 中心设备管理概述](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/)、[如何更新设备固件](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-device-jobs/) |
| 步骤 | LWM2M 是开放移动联盟为 IoT 设备管理提供的协议。 借助 Azure IoT 设备管理可以使用设备作业来与物理设备交互。 请确保云网关实施相应的过程，定期使用 Azure IoT 中心设备管理将设备和其他配置数据保持最新状态。 |

## <a id="controls-policies"></a>确保根据组织的策略在设备上配置终结点安全控制

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | 计算机信任边界 | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | 不适用  |
| 步骤 | 确保根据组织的安全策略，在设备中配置终结点安全控制机制，例如，用于磁盘级加密的 bitlocker、包含更新病毒特征的防病毒软件、基于主机的防火墙，OS 升级、组策略等。 |

## <a id="secure-keys"></a>确保对 Azure 存储访问密钥进行安全管理

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Azure 存储空间 | 
| SDL 阶段               | 部署 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [Azure 存储安全指南 - 管理存储帐户密钥](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| 步骤 | <p>密钥存储：建议将 Azure 存储访问密钥以机密的形式存储在 Azure Key Vault 中，并让应用程序从 Key Vault 中检索密钥。 提出这种建议的原因如下：</p><ul><li>应用程序永远都不需要将存储密钥硬编码于配置文件中，这就消除了某人不需特定权限即可访问密钥的情况。</li><li>可以使用 Azure Active Directory 来控制对密钥的访问。 这意味着，帐户所有者可向需要从 Azure Key Vault 检索密钥的少数应用程序授予访问权限。 如果未获得专门的授权，其他应用程序将无法访问密钥</li><li>密钥重新生成：出于安全原因，建议制定一个过程来重新生成 Azure 存储访问密钥。 《Azure 存储安全指南》参考文章中介绍了为何以及如何规划密钥重新生成的详细信息</li></ul>|

## <a id="cors-storage"></a>确保已在 Azure 存储中启用 CORS 的情况下只允许受信任的来源

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | Azure 存储空间 | 
| SDL 阶段               | 构建 |  
| 适用的技术 | 泛型 |
| 属性              | 不适用  |
| 参考              | [Azure 存储服务的 CORS 支持](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| 步骤 | Azure 存储空间允许你启用 CORS – 跨域资源共享。 对于每个存储帐户，你可以指定可访问该存储帐户中的资源的域。 默认情况下，对所有服务禁用了 CORS。 你可以使用 REST API 或存储客户端库调用某个方法来设置服务策略，以启用 CORS。 |

## <a id="throttling"></a>启用 WCF 的服务限制功能

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | WCF | 
| SDL 阶段               | 构建 |  
| 适用的技术 | .NET Framework 3 |
| 属性              | 不适用  |
| 参考              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| 步骤 | <p>不对系统资源的使用施加限制可能会导致资源耗尽，最终出现拒绝服务。</p><ul><li>**说明：**Windows Communication Foundation (WCF) 提供限制服务请求的功能。 允许过多的客户端请求可能会使系统泛洪并耗尽其资源。 另一方面，只允许向服务发出少量的请求可能会防碍合法用户使用该服务。 应该单独针对每个服务进行优化和配置，以允许适当数量的资源。</li><li>**建议：**启用 WCF 的服务限制功能，并设置适合应用程序的限制。</li></ul>|

### <a name="example"></a>示例
下面是启用了限制的示例配置：
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF - 通过元数据透露信息

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| 组件               | WCF | 
| SDL 阶段               | 构建 |  
| 适用的技术 | .NET Framework 3 |
| 属性              | 不适用  |
| 参考              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| 步骤 | 元数据可帮助攻击者了解系统并规划攻击形式。 WCF 服务可能会配置为公开元数据。 元数据提供详细的服务说明信息，不应在生产环境中广播。 ServiceMetaData 类的 `HttpGetEnabled` / `HttpsGetEnabled` 属性定义服务是否要公开元数据 | 

### <a name="example"></a>示例
以下代码指示 WCF 广播服务的元数据
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
不要在生产环境中广播服务元数据。 将 ServiceMetaData 类的 HttpGetEnabled/HttpsGetEnabled 属性设置为 false。 

### <a name="example"></a>示例
以下代码指示 WCF 不要广播服务的元数据。 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

