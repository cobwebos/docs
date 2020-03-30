---
title: 了解并解决 Azure AD 应用程序代理 CORS 问题
description: 在 Azure AD 应用程序代理中提供对 CORS 的了解，以及如何识别和解决 CORS 问题。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025786"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>了解并解决 Azure 活动目录应用程序代理 CORS 问题

[跨源资源共享 （CORS）](https://www.w3.org/TR/cors/) 有时会对通过 Azure 活动目录应用程序代理发布的应用和 API 带来挑战。 本文讨论 Azure AD 应用程序代理 CORS 问题和解决方案。

浏览器安全性通常阻止网页向另一个域发出 AJAX 请求。 此限制称为*同源策略*，并防止恶意站点从其他站点读取敏感数据。 但是，有时您可能希望让其他站点调用您的 Web API。 CORS 是一个 W3C 标准，它允许服务器放松同源策略，并允许某些跨源请求，同时拒绝其他请求。

## <a name="understand-and-identify-cors-issues"></a>了解并识别 CORS 问题

如果两个 URL 具有相同的方案、主机和端口[（RFC 6454），](https://tools.ietf.org/html/rfc6454)则它们具有相同的源，例如：

-   http：\//contoso.com/foo.html
-   http：\//contoso.com/bar.html

以下 URL 与前两个 URL 的源不同：

-   http：\//contoso.net - 不同的域
-   http：\//contoso.com:9000/foo.html - 不同的端口
-   https：\//contoso.com/foo.html - 不同的方案
-   http：\//www.contoso.com/foo.html - 不同的子域

同源策略阻止应用从其他源访问资源，除非它们使用正确的访问控制标头。 如果 CORS 标头不存在或不正确，则跨源请求将失败。 

您可以使用浏览器调试工具识别 CORS 问题：

1. 启动浏览器并浏览到 Web 应用。
1. 按**F12**可启动调试控制台。
1. 尝试重现事务，并查看控制台消息。 CORS 冲突会产生有关源的控制台错误。

在下面的屏幕截图中，选择 **"试用"** 按钮会导致 CORS 错误消息 https：\//corswebclient-contoso.msappproxy.net 在访问-控制-允许源标头中找不到。

![CORS 问题](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>应用程序代理的 CORS 挑战

下面的示例显示了典型的 Azure AD 应用程序代理 CORS 方案。 内部服务器托管一个**CORSWeb服务WebAPI**控制器，以及一个调用**CORSWeb服务的****CORSWeb客户端**。 有一个AJAX请求从**CORSWeb客户端****到CORSWeb服务**。

![本地同源请求](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 应用在本地托管时有效，但在通过 Azure AD 应用程序代理发布时无法加载或错误。 如果您通过应用程序代理将 CORSWebClient 和 CORSWeb 服务应用单独发布为不同的应用，则这两个应用将托管在不同的域中。 从 CORSWebClient 到 CORSWeb 服务的 AJAX 请求是一个跨源请求，并且失败。

![应用程序代理 CORS 请求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>应用程序代理 CORS 问题的解决方案

您可以通过多种方式中的任何一种方式解决前面的 CORS 问题。

### <a name="option-1-set-up-a-custom-domain"></a>选项 1：设置自定义域

使用 Azure AD 应用程序代理[自定义域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)从同一源发布，而无需对应用源、代码或标头进行任何更改。 

### <a name="option-2-publish-the-parent-directory"></a>选项 2：发布父目录

发布两个应用的父目录。 如果 Web 服务器上只有两个应用，则此解决方案效果特别好。 您可以发布公共父目录，而不是单独发布每个应用，这将导致相同的源。

以下示例显示 CORSWebClient 应用的门户 Azure AD 应用程序代理页。  当**内部 URL**设置为*contoso.com/CORSWebClient*时，应用无法成功向*contoso.com/CORSWebService*目录发出请求，因为它们是跨源的。 

![单独发布应用](./media/application-proxy-understand-cors-issues/image4.png)

相反，将**内部 URL**设置为发布父目录，其中包括*CORSWebClient*和*CORSWeb 服务*目录：

![发布父目录](./media/application-proxy-understand-cors-issues/image5.png)

生成的应用 URL 可有效解决 CORS 问题：

- https：\//corswebclient-contoso.msappproxy.net/CORSWebService
- https：\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>选项 3：更新 HTTP 标头

在 Web 服务上添加自定义 HTTP 响应标头以匹配源请求。 对于在互联网信息服务 （IIS） 中运行的网站，请使用 IIS 管理器修改标头：

![在 IIS 管理器中添加自定义响应标头](./media/application-proxy-understand-cors-issues/image6.png)

此修改不需要任何代码更改。 您可以在 Fiddler 跟踪中验证它：

**过帐标题添加**\
HTTP/1.1 200 OK。
缓存控制：无缓存*
普拉格玛：无缓存*
内容类型：文本/纯;字符_utf-8_
过期： -1*
变化：接受编码*
服务器：微软-IIS/8.5 微软-HTTPAPI/2.0*
**访问控制-允许-原点：https\:/corswebclient-contoso.msappproxy.net**\
X-AspNet 版本：4.0.30319*
X 供电：ASP.NET*
内容长度： 17

### <a name="option-4-modify-the-app"></a>选项 4：修改应用

您可以通过添加具有适当值的访问控制允许源标头来更改应用以支持 CORS。 添加标头的方式取决于应用的代码语言。 更改代码是最不推荐的选项，因为它需要付出最大的努力。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>选项 5：延长访问令牌的生存期

某些 CORS 问题无法解决，例如，当应用重定向到*login.microsoftonline.com*进行身份验证时，访问令牌将过期。 然后，CORS 调用将失败。 此方案的解决方法是延长访问令牌的生存期，以防止它在用户会话期间过期。 有关如何执行此操作的详细信息，请参阅 Azure AD[中的可配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>请参阅
- [教程：在 Azure 活动目录中添加本地应用程序，以便通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md) 
- [规划 Azure AD 应用程序代理部署](application-proxy-deployment-plan.md) 
- [通过 Azure 活动目录应用程序代理远程访问本地应用程序](application-proxy.md) 
