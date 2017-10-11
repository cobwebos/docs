---
title: "异常管理 - Microsoft 威胁建模工具 - Azure | Microsoft 文档"
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
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: bbf357b902474a1812eb7a5a2c914d0c8b91934b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="security-frame-exception-management--mitigations"></a>安全框架：异常管理 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - 不要在配置文件中包含 serviceDebug 节点](#servicedebug)</li><li>[WCF - 不要在配置文件中包含 serviceMetadata 节点](#servicemetadata)</li></ul> |
| **Web API** | <ul><li>[确保在 ASP.NET Web API 中进行适当的异常处理](#exception)</li></ul> |
| **Web 应用程序** | <ul><li>[不要在错误消息中公开安全详细信息](#messages)</li><li>[实现默认错误处理页](#default)</li><li>[在 IIS 中将部署方法设置为 Retail](#deployment)</li><li>[异常应安全失败](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - 不要在配置文件中包含 serviceDebug 节点

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型、NET Framework 3 |
| **属性**              | 不适用  |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| **步骤** | 可将 Windows Communication Framework (WCF) 服务配置为公开调试信息。 不应在生产环境中使用调试信息。 `<serviceDebug>` 标记定义是否为 WCF 服务启用调试信息功能。 如果 includeExceptionDetailInFaults 特性设置为 true，则将来自应用程序的异常信息返回到客户端。 攻击者可以利用他们从调试输出中获取的附加信息，针对框架、数据库或者应用程序使用的其他资源展开攻击。 |

### <a name="example"></a>示例
以下配置文件包含 `<serviceDebug>` 标记： 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
在服务中禁用调试信息。 可通过在应用程序的配置文件中删除 `<serviceDebug>` 标记来实现此目的。 

## <a id="servicemetadata"></a>WCF - 不要在配置文件中包含 serviceMetadata 节点

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 泛型、NET Framework 3 |
| **参考**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx)、[巩固王国](https://vulncat.fortify.com/en/vulncat/index.html) |
| **步骤** | 有关服务的公开信息可能会给攻击者提供有用的洞察数据，使他们知道如何攻击服务。 `<serviceMetadata>` 标记会启用元数据发布功能。 服务元数据可能包含不应该提供给公众访问的敏感信息。 最起码，应该只允许受信任的用户访问元数据，确保不必要的信息不会公开。 当然，更好的做法是完全禁用元数据发布功能。 安全的 WCF 配置不包含 `<serviceMetadata>` 标记。 |

## <a id="exception"></a>确保在 ASP.NET Web API 中进行适当的异常处理

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | MVC 5、MVC 6 |
| **属性**              | 不适用  |
| **参考**              | [ASP.NET Web API 中的异常处理](http://www.asp.net/web-api/overview/error-handling/exception-handling)、[ASP.NET Web API 中的模型验证](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **步骤** | 默认情况下，ASP.NET Web API 中大多数未捕获的异常将转换成状态代码为 `500, Internal Server Error` 的 HTTP 响应|

### <a name="example"></a>示例
若要控制 API 返回的状态代码，可按如下所示使用 `HttpResponseException`： 
```C#
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>示例
若要进一步控制异常响应，可按如下所示使用 `HttpResponseMessage` 类： 
```C#
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
若要捕捉类型不是 `HttpResponseException` 的未经处理的异常，可以使用异常筛选器。 异常筛选器实现 `System.Web.Http.Filters.IExceptionFilter` 接口。 编写异常筛选器的最简单方法是从 `System.Web.Http.Filters.ExceptionFilterAttribute` 类派生，并重写 OnException 方法。 

### <a name="example"></a>示例
下面是可将 `NotImplementedException` 异常转换为 HTTP 状态代码 `501, Not Implemented` 的筛选器： 
```C#
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

可通过多种方法注册 Web API 异常筛选器：
- 按操作
- 按控制器
- 全局

### <a name="example"></a>示例
要将筛选器应用到特定的操作，请将筛选器作为特性添加到该操作： 
```C#
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>示例
要将筛选器应用到 `controller` 中的所有操作，请将筛选器作为特性添加到 `controller` 类： 

```C#
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>示例
要将筛选器全局应用到所有 Web API 控制器，请将筛选器的实例添加到 `GlobalConfiguration.Configuration.Filters` 集合。 此集合中的异常筛选器将应用到任何 Web API 控制器操作。 
```C#
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>示例
对于模型验证，可按如下所示，将模型状态传递给 CreateErrorResponse 方法： 
```C#
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

请查看参考部分中的链接，了解有关 ASP.Net Web API 中异常处理和模型验证的更多详细信息 

## <a id="messages"></a>不要在错误消息中公开安全详细信息

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | 不适用  |
| **步骤** | <p>常规错误消息将直接提供给用户，其中不包含敏感的应用程序数据。 敏感数据的示例包括：</p><ul><li>服务器名称</li><li>连接字符串</li><li>用户名</li><li>密码</li><li>SQL 过程</li><li>动态 SQL 失败详细信息</li><li>堆栈跟踪和代码行</li><li>内存中存储的变量</li><li>驱动器和文件夹位置</li><li>应用程序安装点</li><li>主机配置设置</li><li>其他内部应用程序详细信息</li></ul><p>捕获应用程序中的所有错误、提供常规错误消息，以及在 IIS 中启用自定义错误，都有助于防止信息泄漏。 SQL Server 数据库和 .NET 异常处理以及其他错误处理体系结构提供的信息特别详细，能够为恶意用户分析应用程序带来极大的帮助。 不要直接显示派生自 .NET Exception 类的类的内容，确保采取适当的异常处理，避免无意中向用户直接引发意外的异常。</p><ul><li>直接向用户提供常规错误消息时，应该去掉异常/错误消息中直接显示的具体详细信息。</li><li>不要直接向用户显示 .NET exception 类的内容</li><li>捕获所有错误消息，在适当的情况下，通过发送到应用程序客户端的常规错误消息来通知用户</li><li>不要直接向用户公开 Exception 类的内容，尤其是 `.ToString()` 的返回值，或者 Message 或 StackTrace 属性的值。 安全记录此信息，向用户显示一般性的消息</li></ul>|

## <a id="default"></a>实现默认错误处理页

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [编辑 ASP.NET 错误页设置对话框](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **步骤** | <p>如果 ASP.NET 应用程序失败并导致 HTTP/1.x 500 内部服务器错误，或者某项功能配置（例如请求筛选）阻止显示页面，会生成错误消息。 管理员可以选择是要让应用程序向客户端显示友好的消息、向客户端显示详细的错误消息，还是只向 localhost 显示详细的错误消息。 web.config 中的 <customErrors> 标记有三种模式：</p><ul><li>**On：**指定启用自定义错误。 如果未指定 defaultRedirect 特性，用户将看到常规错误。 将向远程客户端和本地主机显示自定义错误</li><li>**Off：**指定禁用自定义错误。 将向远程客户端和本地主机显示详细的 ASP.NET 错误</li><li>**RemoteOnly：**指定只向远程客户端显示自定义错误，向本地主机显示 ASP.NET 错误。 这是默认值</li></ul><p>打开应用程序/站点的 `web.config`文件，确保该标记中定义了 `<customErrors mode="RemoteOnly" />` 或 `<customErrors mode="On" />`。</p>|

## <a id="deployment"></a>在 IIS 中将部署方法设置为 Retail

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [部署元素（ASP.NET 设置架构）](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **步骤** | <p>`<deployment retail>` 开关旨在由 IIS 生产服务器使用。 使用此开关可以帮助应用程序尽量以最佳的性能运行，同时，可通过禁用应用程序在页面中生成跟踪输出的功能、禁用向最终用户显示详细错误消息的功能，以及禁用调试开关，来最大程度地减少泄露安全信息的可能性。</p><p>通常，主要面向开发人员的开关和选项（例如，对失败的请求进行跟踪和调试）是在现行开发的过程中启用的。 建议将任何生产服务器上的部署方法设置为 retail。 打开 machine.config 文件，确保 `<deployment retail="true" />` 保持设置为 true。</p>|

## <a id="fail"></a>异常应安全失败

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 不适用  |
| **参考**              | [安全失败](https://www.owasp.org/index.php/Fail_securely) |
| **步骤** | 应用程序应安全失败。 对于根据所做的特定决策返回布尔值的任何方法，应该谨慎创建异常块。 许多逻辑错误就是因为在编写异常块时漫不经心，使安全问题日积月累造成的。|

### <a name="example"></a>示例
```C#
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
如果发生某种异常，上述方法始终返回 True。 如果最终用户提供了浏览器支持的、但 `Uri()` 构造函数不支持的错误格式 URL，则会引发异常，受害者会转到有效但格式不当的 URL。 