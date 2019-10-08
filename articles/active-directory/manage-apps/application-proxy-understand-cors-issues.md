---
title: 了解和解决 Azure AD 应用程序代理 CORS 问题
description: 了解 Azure AD 应用程序代理中的 CORS，以及如何识别和解决 CORS 问题。
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
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025786"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>了解和解决 Azure Active Directory 应用程序代理 CORS 问题

[跨域资源共享（CORS）](https://www.w3.org/TR/cors/) can 有时会对通过 Azure Active Directory 应用程序代理发布的应用和 api 提出挑战。 本文讨论 Azure AD 应用程序代理 CORS 问题和解决方案。

浏览器安全性通常会阻止网页向另一个域发出 AJAX 请求。 此限制称为“同源策略”，可防止恶意站点读取另一个站点中的敏感数据。 但是，有时你可能希望让其他站点调用你的 web API。 CORS 是一种 W3C 标准，可让服务器放宽相同的源策略，并允许一些跨域请求，同时拒绝其他请求。

## <a name="understand-and-identify-cors-issues"></a>了解并识别 CORS 问题

如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源，例如：

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

以下 Url 的来源不同于前两个 Url：

-   http： \//contoso. net-不同域
-   http： \//contoso.com:9000/foo.html-不同的端口
-   https @no__t： 0/contoso.com/foo.html-不同方案
-   http @no__t： 0/www.contoso.com/foo.html-不同的子域

相同源策略阻止应用从其他源访问资源，除非它们使用正确的访问控制标头。 如果 CORS 标头不存在或不正确，则跨源请求会失败。 

可以使用浏览器调试工具来确定 CORS 问题：

1. 启动浏览器并浏览到 web 应用。
1. 按**F12**打开调试控制台。
1. 尝试重现该事务，并查看控制台消息。 CORS 冲突产生了有关源的控制台错误。

在以下屏幕截图中，选择 "试用" 按钮会导致 CORS 错误消息：在访问控制--corswebclient 标头中找**不**到 https： \//。

![CORS 问题](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>应用程序代理的 CORS 挑战

以下示例显示了一个典型的 Azure AD 应用程序代理 CORS 方案。 内部服务器承载**CORSWebService** web API 控制器，以及调用**CORSWebService**的**CORSWebClient** 。 有一个从**CORSWebClient**到**CORSWebService**的 AJAX 请求。

![本地同一源请求](./media/application-proxy-understand-cors-issues/image1.png)

当你在本地托管该应用程序时，CORSWebClient 应用程序将正常工作，但在通过 Azure AD 应用程序代理发布时无法加载或错误。 如果将 CORSWebClient 和 CORSWebService 应用作为不同应用通过应用程序代理单独发布，则这两个应用托管在不同的域中。 从 CORSWebClient 到 CORSWebService 的 AJAX 请求是一个跨源请求，并且失败。

![应用程序代理 CORS 请求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>适用于应用程序代理 CORS 问题的解决方案

可以通过多种方式解决上述 CORS 问题。

### <a name="option-1-set-up-a-custom-domain"></a>选项 1：设置自定义域

使用 Azure AD 应用程序代理[自定义域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)从同一源发布，无需对应用程序源、代码或标头进行任何更改。 

### <a name="option-2-publish-the-parent-directory"></a>选项 2：发布父目录

发布这两个应用的父目录。 如果 web 服务器上只有两个应用，则此解决方案特别适用。 不是单独发布每个应用，而是发布公共父目录，这会产生相同的源。

以下示例显示 CORSWebClient 应用的门户 Azure AD 应用程序代理 "页。  当**内部 URL**设置为*contoso.com/CORSWebClient*时，该应用无法对*contoso.com/CORSWebService*目录进行成功请求，因为它们是跨域的。 

![单独发布应用](./media/application-proxy-understand-cors-issues/image4.png)

相反，请设置**内部 URL**以发布父目录，其中包括*CORSWebClient*和*CORSWebService*目录：

![发布父目录](./media/application-proxy-understand-cors-issues/image5.png)

生成的应用 Url 可以有效地解决 CORS 问题：

- https： \//corswebclient/msappproxy/CORSWebService
- https： \//corswebclient/msappproxy/CORSWebClient

### <a name="option-3-update-http-headers"></a>选项 3：更新 HTTP 标头

添加 web 服务的自定义 HTTP 响应标头，以匹配源请求。 对于在 Internet Information Services （IIS）中运行的网站，请使用 IIS 管理器来修改标头：

![在 IIS 管理器中添加自定义响应标头](./media/application-proxy-understand-cors-issues/image6.png)

此修改不需要更改任何代码。 可以在 Fiddler 跟踪中对其进行验证：

**将标头添加到**\
HTTP/1.1 200 良好 \
缓存-控制：非缓存 \
Pragma：非缓存 \
Content-type：文本/无格式;字符集 = utf-8 \
过期时间：-1 \
大接受编码 \
服务器:Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**访问控制-允许-源： https @ no__t-1//corswebclient-msappproxy**\
X-AspNet 版本：4.0.30319\
X-通过：ASP.NET\
内容长度：17

### <a name="option-4-modify-the-app"></a>选项 4：修改应用

可以通过使用适当的值添加 "访问控制-允许源" 标头来更改应用以支持 CORS。 添加标头的方式取决于应用程序的代码语言。 更改代码是最不推荐的选项，因为这需要的工作量最大。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>选项5：延长访问令牌的生存期

无法解决某些 CORS 问题，例如当应用重定向到*login.microsoftonline.com*进行身份验证时，访问令牌过期。 否则，CORS 调用将失败。 此方案的一种解决方法是扩展访问令牌的生存期，以防止它在用户会话期间过期。 有关如何执行此操作的详细信息，请参阅[Azure AD 中的可配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>请参阅
- [教程：添加本地应用程序，通过应用程序代理远程访问 Azure Active Directory @ no__t 中的应用程序代理-0 
- [规划 Azure AD 应用程序代理部署](application-proxy-deployment-plan.md) 
- [通过 Azure Active Directory 应用程序代理远程访问本地应用程序](application-proxy.md) 
