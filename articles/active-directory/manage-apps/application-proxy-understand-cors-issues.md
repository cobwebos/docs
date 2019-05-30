---
title: 了解并解决 Azure AD 应用程序代理 CORS 问题
description: 提供了在 Azure AD 应用程序代理，以及如何识别和解决 CORS 问题 CORS 的了解。
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
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399346"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>了解并解决 Azure Active Directory 应用程序代理 CORS 问题

[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) 有时可以提供有关应用和 Api 通过 Azure Active Directory 应用程序代理发布的问题。 本文介绍 Azure AD 应用程序代理 CORS 问题和解决方案。

浏览器安全性通常可防止 web 页面到另一个域发出 AJAX 请求。 此限制称为“同源策略”  ，可防止恶意站点读取另一个站点中的敏感数据。 但是，有时你可能想要让调用 web API 的其他站点。 CORS 是一项 W3C 标准，可让服务器放宽同域策略，并允许同时拒绝另一些跨域请求。

## <a name="understand-and-identify-cors-issues"></a>了解和确定 CORS 问题

如果它们具有相同方案、 主机和端口，两个 Url 将具有相同的原点 ([RFC 6454](https://tools.ietf.org/html/rfc6454))，如：

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

以下 Url 有两个相对上一不同的源：

-   http:\//contoso.net-不同的域
-   http:\//contoso.com:9000/foo.html-不同的端口
-   https:\//contoso.com/foo.html-不同的方案
-   http:\//www.contoso.com/foo.html-不同的子域

同域策略阻止应用从其他来源访问资源，除非它们使用正确的访问控制标头。 如果 CORS 标头不存在或不正确，跨域请求失败。 

可以通过使用浏览器调试工具来识别 CORS 问题：

1. 启动浏览器并浏览到 web 应用。
1. 按**F12**以打开调试控制台。
1. 尝试再现该事务，并查看控制台消息。 CORS 冲突会生成关于源的控制台错误。

在以下屏幕截图中，选择**试用**按钮导致 CORS 错误消息的 https:\//corswebclient-contoso.msappproxy.net 找不到访问控制的允许的域标头中。

![CORS 问题](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>使用应用程序代理的 CORS 挑战

下面的示例演示一个典型方案中 Azure AD 应用程序代理 CORS。 内部服务器主机**CORSWebService** web API 控制器和一个**CORSWebClient**调用 **CORSWebService**。 没有从 AJAX 请求**CORSWebClient**到**CORSWebService**。

![在本地同域请求](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 应用托管时它在本地，但加载失败，或者出现错误时通过 Azure AD 应用程序代理发布的工作方式。 如果为应用程序代理通过不同的应用程序的单独发布的 CORSWebClient 和 CORSWebService 应用，两个应用程序托管在不同的域。 从 CORSWebClient CORSWebService 到 AJAX 请求是跨域请求，并失败。

![应用程序代理 CORS 请求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>应用程序代理 CORS 问题的解决方案

可以解决其中的任何一种几种方法的前面 CORS 问题。

### <a name="option-1-set-up-a-custom-domain"></a>选项 1：设置自定义域

使用 Azure AD 应用程序代理[的自定义域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)从同一个原始发布而无需对应用程序来源、 代码或标头进行任何更改。 

### <a name="option-2-publish-the-parent-directory"></a>选项 2：发布的父目录

发布这两个应用的父目录。 此解决方案发挥作用，尤其是如果你的 web 服务器上的只有两个应用。 而不是单独发布每个应用，你可以发布公共父目录，这会导致相同的原点。

以下示例显示在门户 CORSWebClient 应用的 Azure AD 应用程序代理页。  当**内部 URL**设置为*contoso.com/CORSWebClient*，应用不能进行到成功的请求*contoso.com/CORSWebService*目录中，因为它们是跨域。 

![将逐个发布应用](./media/application-proxy-understand-cors-issues/image4.png)

与此相反，设置**内部 URL**发布父目录，包括这两个*CORSWebClient*并*CORSWebService*目录：

![发布父目录](./media/application-proxy-understand-cors-issues/image5.png)

生成的应用程序 Url 有效地解决 CORS 问题：

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>选项 3：更新 HTTP 标头

Web 服务，以匹配源请求上添加自定义 HTTP 响应标头。 对于运行 Internet 信息服务 (IIS) 中的网站，使用 IIS 管理器修改标头：

![在 IIS 管理器中添加自定义响应标头](./media/application-proxy-understand-cors-issues/image6.png)

此修改不需要更改任何代码。 Fiddler 跟踪中，可以验证它：

**Post 标头添加**\
HTTP/1.1 200 OK\
缓存控制： 无 cache\
杂注： 否 cache\
内容类型： text/plain;charset = utf-8 \
过期:-1
而有所不同：接受 Encoding\
服务器:Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**访问控制的允许的域： https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version:4.0.30319\
X-提供支持的情况：ASP.NET\
内容长度：17

### <a name="option-4-modify-the-app"></a>选项 4：修改应用程序

您可以更改您的应用程序以添加具有适当的值的访问控制的允许的域标头支持 CORS。 若要添加该标头的方式取决于应用程序的代码语言。 更改的代码是最推荐的选项，因为它要求的大多数工作。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>选项 5:扩展的访问令牌的生存期

CORS 的一些问题不能为已解决，例如当您的应用程序将重定向到*login.microsoftonline.com*进行身份验证，且访问令牌过期。 CORS 调用，则将失败。 此方案的解决方法是扩展的访问令牌，以防止过期用户会话期间的生存期。 有关如何执行此操作的详细信息，请参阅[Azure AD 中的可配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>另请参阅
- [教程：在 Azure Active Directory 中添加用于通过应用程序代理远程访问的本地应用程序](application-proxy-add-on-premises-application.md) 
- [计划的 Azure AD 应用程序代理部署](application-proxy-deployment-plan.md) 
- [如何通过 Azure Active Directory 应用程序代理的本地应用程序的远程访问](application-proxy.md) 
