---
title: "转换链接和 URL Azure AD 应用代理 | Microsoft Docs"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: d716476deb81f6627cf03401f78ab399ae940e68
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017

---

# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理重定向已发布应用的硬编码链接

Azure AD 应用程序代理使本地应用对远程或在自己设备上的用户可用。 但是，某些应用开发时考虑的是本地受众，这通常表示本地链接嵌入在 HTML 中，且在远程使用应用时运行不正常。 如果多个本地应用程序指向彼此，且用户期望使用链接在应用间移动，而不是在每个应用的外部 URL 中输入内容，这种情况最为常见。

应用程序代理链接转换功能就是为解决此问题而开发的。 如果具有直接指向内部终结点或端口的应用，可以将这些内部 URL 映射到已发布的外部应用程序代理服务器 URL 中。 通过 HTML、CSS 启用链接转换和应用程序代理搜索，选择已发布内部链接的 JavaScript 标记，然后进行转换，可让用户获得不间断的体验。

>[!NOTE]
>链接转换功能适用于无论什么原因都无法使用自定义域的租户，以使应用具有相同的内部和外部 URL。 启用此功能之前，请查看 [Azure AD 应用程序代理中的自定义域](active-directory-application-proxy-custom-domains.md)对你是否适用。

## <a name="how-link-translation-works"></a>链接转换工作原理

身份验证后，如果代理服务器将应用程序数据传递给用户，应用程序代理会扫描应用程序，确定硬编码链接并将其替换为其各自已发布的外部 URL。

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
- 从其他程序打开的内部链接。 不会转换通过电子邮件或即时消息发送或其他文档中包含的链接。 用户需要了解转到外部 URL。

如果需要支持这两个方案之一，可以使用相同的内部和外部 URL，如此便无需转换链接。  

## <a name="enable-link-translation"></a>启用链接转换

链接转换入门就像单击按钮一样简单：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 转到“Azure Active Directory” > “企业应用程序” > “所有应用程序”选择要管理的应用，再单击“应用程序代理”。
3. 将“转换应用程序主体中的 URL”改为“是”。

   ![选择“是”可转换应用程序主体中的 URL](./media/application-proxy-link-translation/select_yes.png)。
4. 单击“保存”应用所做的更改。

现在，当用户访问此应用程序时，代理将自动扫描租户上通过应用程序代理发布的内部 URL。

## <a name="send-feedback"></a>发送反馈

我们希望获得你的帮助，使此功能适用于所有应用。 我们搜索了 HTML 和 CSS 中 30 个以上的标记，并正在考虑支持哪些 JavaScript 用例。 如果有尚未进行转换的生成链接示例，请向[应用程序代理反馈](mailto:aadapfeedback@microsoft.com)发送一个代码片段。 

## <a name="next-steps"></a>后续步骤
- [通过 Azure AD 应用程序代理使用自定义域，以便具有相同的内部和外部 URL](active-directory-application-proxy-custom-domains.md)

