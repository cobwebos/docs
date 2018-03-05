---
title: "转换链接和 URL Azure AD 应用代理 | Microsoft Docs"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: d5d704dac58d65dd7d62bc3eca400f9541714d5d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理重定向已发布应用的硬编码链接

Azure AD 应用程序代理使本地应用对远程或在自己设备上的用户可用。 但是，某些应用已通过嵌入到 HTML 中的本地链接开发。 远程使用应用时，这些链接将无法正常跳转。 如果有多个指向彼此的本地应用程序，你的用户会需要这些链接在他们不在办公室时能继续正常使用。 

确保链接在企业网络内部和外部都能够正常工作的最佳方式是，将应用的外部 URL 与其内部 URL 配置为相同的。 使用[自定义域](active-directory-application-proxy-custom-domains.md)来配置你的外部 URL，以获取企业域名，而不是默认的应用程序代理域。

如果不能在租户中使用自定义域，则应用程序代理的链接转换功能将使链接保持正常运作，而不管用户在哪里。 如果具有直接指向内部终结点或端口的应用，可以将这些内部 URL 映射到已发布的外部应用程序代理服务器 URL 中。 如果启用了链接转换，并且应用程序代理通过 HTML 和 CSS 搜索发布的内部链接，则应用程序代理服务将转换这些链接，以便让用户获得不间断的体验。

>[!NOTE]
>链接转换功能适用于无论什么原因都无法使用自定义域的租户，以使其应用具有相同的内部和外部 URL。 启用此功能之前，请查看 [Azure AD 应用程序代理中的自定义域](active-directory-application-proxy-custom-domains.md)对你是否适用。
>
>或者，如果需要配置链接转换的应用程序是 SharePoint，请参阅[配置 SharePoint 2013 的备用访问映射](https://technet.microsoft.com/library/cc263208.aspx)，以获取映射链接的另一种方法。

## <a name="how-link-translation-works"></a>链接转换工作原理

身份验证后，如果代理服务器将应用程序数据传递给用户，应用程序代理会扫描应用程序，确定硬编码链接并将其替换为其各自已发布的外部 URL。

应用程序代理假定应用程序采用 UTF-8 编码。 如果不是这种情况，则在 http 响应标头中指定编码类型，如 `Content-Type:text/html;charset=utf-8`。

### <a name="which-links-are-affected"></a>哪些链接受影响？

链接转换功能仅查找位于应用正文的代码标记中的链接。 应用程序代理具有一个单独的功能，用于转换标头中的 cookie 或 URL。 

本地应用程序中有两种常见类型的内部链接：

- 相对内部链接，可指向本地文件结构（如 `/claims/claims.html`）中的共享资源。 这些链接在通过应用程序代理发布的应用中自动运行，并且在有无链接转换时均能继续运行。 
- 硬编码内部链接，指向其他本地应用（如 `http://expenses`）或已发布文件（如 `http://expenses/logo.jpg`）。 链接转换功能对硬编码内部链接进行操作，并将其更改为指向远程用户需要访问的外部 URL。

### <a name="how-do-apps-link-to-each-other"></a>应用如何彼此链接？

链接转换对每个应用程序均启用，以便可以按应用级别控制用户体验。 如果要将该应用的链接，而不是指向该应用的链接进行转换，请启用应用的链接转换功能。 

例如，假设有三个通过应用程序代理发布的应用程序，这些应用程序均彼此链接：Benefits、Expenses 和 Travel。 还有第四个应用，Feedback，但它不是通过应用程序代理发布的。

如果启用 Benefits 应用的链接转换，Expenses 和 Travel 的链接将重定向到这些应用的外部 URL，但不会重定向 Feedback 的链接，因为没有任何外部 URL。 从 Expenses 和 Travel 返回 Benefits 的链接无法运行，因为尚未启用这两个应用的链接转换。

![启用链接转换后，Benefits 到其他应用的链接](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>哪些链接不转换？

为了提高性能和安全性，不会转换某些链接：

- 代码标记外的链接。 
- 不在 HTML 或 CSS 中的链接。 
- 从其他程序打开的内部链接。 不会转换通过电子邮件或即时消息发送或其他文档中包含的链接。 用户需要了解转到外部 URL。

如果需要支持这两个方案之一，请使用相同的内部和外部 URL，而不是链接转换。  

## <a name="enable-link-translation"></a>启用链接转换

链接转换入门就像单击按钮一样简单：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 转到“Azure Active Directory” > “企业应用程序” > “所有应用程序”选择要管理的应用，再单击“应用程序代理”。
3. 将“转换应用程序主体中的 URL”改为“是”。

   ![选择“是”可转换应用程序主体中的 URL](./media/application-proxy-link-translation/select_yes.png)。
4. 单击“保存”应用所做的更改。

现在，当用户访问此应用程序时，代理将自动扫描租户上通过应用程序代理发布的内部 URL。

## <a name="send-feedback"></a>发送反馈

我们希望获得你的帮助，使此功能适用于所有应用。 在 HTML 和 CSS 中搜索 30 多个标记。 如果有尚未进行转换的生成链接示例，请向[应用程序代理反馈](mailto:aadapfeedback@microsoft.com)发送一个代码片段。 

## <a name="next-steps"></a>后续步骤
[通过 Azure AD 应用程序代理使用自定义域](active-directory-application-proxy-custom-domains.md)，以便具有相同的内部和外部 URL

[配置 SharePoint 2013 的备用访问映射](https://technet.microsoft.com/library/cc263208.aspx)
