---
title: 转换链接和 URL Azure AD 应用代理 | Microsoft Docs
description: 介绍有关 Azure AD 应用程序代理连接器的基础知识。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 618df9b4bcc4a1b6f44d9cabc29c797a2cabcc80
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366786"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理重定向已发布应用的硬编码链接

Azure AD 应用程序代理使本地应用对远程或在自己设备上的用户可用。 但是，某些应用已通过嵌入到 HTML 中的本地链接开发。 远程使用应用时，这些链接将无法正常跳转。 如果有多个指向彼此的本地应用程序，你的用户会需要这些链接在他们不在办公室时能继续正常使用。 

确保链接在企业网络内部和外部都能够正常工作的最佳方式是，将应用的外部 URL 与其内部 URL 配置为相同的。 使用[自定义域](application-proxy-configure-custom-domain.md)来配置你的外部 URL，以获取企业域名，而不是默认的应用程序代理域。


如果无法在租户中使用自定义域，还有其他几个选项可提供此功能。 所有这些选项也都与自定义域兼容且彼此兼容，因此可以根据需要配置自定义域和其他解决方案。 

**选项 1：使用 Managed Browser** - 此解决方案仅适用于计划推荐或要求用户通过 Intune Managed Browser 访问应用程序的情况。 它处理所有已发布的 URL。 

**选项 2：使用 MyApps 扩展** - 此解决方案要求用户安装客户端浏览器扩展，但它将处理所有已发布的 URL，并可与大多数热门浏览器一起使用。 

**选项 3：使用链接转换设置** - 这是一种管理端设置，对用户不可见。 但是，它仅处理 HTML 和 CSS 中的 URL。 例如，不适用于通过 Javascript 生成的硬编码内部 URL。  

无论用户位于何处，这三个功能都可使链接保持正常工作。 如果具有直接指向内部终结点或端口的应用，可以将这些内部 URL 映射到已发布的外部应用程序代理服务器 URL 中。 

 
> [!NOTE]
> 最后一个选项只适用于出于任何原因无法使用自定义域来使其应用具有相同的内部和外部 URL 的租户。 启用此功能之前，请查看 [Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)对你是否适用。 

>或者，如果需要配置链接转换的应用程序是 SharePoint，请参阅[配置 SharePoint 2013 的备用访问映射](https://technet.microsoft.com/library/cc263208.aspx)，以获取映射链接的另一种方法。 

 
### <a name="option-1-intune-managed-browser-integration"></a>选项 1：Intune Managed Browser 集成 

可以使用 Intune Managed Browser 进一步保护应用程序和内容。 若要使用此解决方案，需要求/建议用户通过 Intune Managed Browser 访问应用程序。 随应用程序代理一起发布的所有内部 URL 都将由 Managed Browser 识别并重定向到相应的外部 URL。 这可确保所有硬编码内部 URL 正常运行，并且如果用户在浏览器中直接键入该内部 URL，即使该用户是远程用户，该 URL 也可正常运行。  

若要了解详细信息（包括如何配置此选项），请参阅 [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) 文档。  

### <a name="option-2-myapps-browser-extension"></a>选项 2：MyApps 浏览器扩展 

通过 MyApps 浏览器扩展，随应用程序代理一起发布的所有内部 URL 都将由该扩展识别并重定向到相应的外部 URL。 这可确保所有硬编码内部 URL 正常运行，并且如果用户在浏览器的地址栏中直接键入该内部 URL，即使该用户是远程用户，该 URL 也可正常运行。  

若要使用此功能，用户需要下载该扩展并登录。 管理员或用户不需要进行其他配置。 

 

### <a name="option-3-link-translation-setting"></a>选项 3：链接转换设置 

如果启用了链接转换，应用程序代理服务通过 HTML 和 CSS 搜索发布的内部链接并对它们进行转换，以便让用户获得不间断的体验。 



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

![启用链接转换后，Benefits 到其他应用的链接](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

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

   ![选择“是”可转换应用程序主体中的 URL](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)。
4. 单击“保存”应用所做的更改。

现在，当用户访问此应用程序时，代理将自动扫描租户上通过应用程序代理发布的内部 URL。

## <a name="send-feedback"></a>发送反馈

我们希望获得你的帮助，使此功能适用于所有应用。 在 HTML 和 CSS 中搜索 30 多个标记。 如果有尚未进行转换的生成链接示例，请向[应用程序代理反馈](mailto:aadapfeedback@microsoft.com)发送一个代码片段。 

## <a name="next-steps"></a>后续步骤
[通过 Azure AD 应用程序代理使用自定义域](application-proxy-configure-custom-domain.md)，以便具有相同的内部和外部 URL

[配置 SharePoint 2013 的备用访问映射](https://technet.microsoft.com/library/cc263208.aspx)
