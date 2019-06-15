---
title: 调试基于 SAML 的单一登录 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录。
services: active-directory
author: rwike77
documentationcenter: na
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0273a0d35d2b4d69f74b1acd8bc2b1d7174810cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111486"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录

了解如何在 Azure Active Directory (Azure AD) 中查找和解决支持[安全断言标记语言 (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 的应用程序的[单一登录](../manage-apps/what-is-single-sign-on.md)问题。 

## <a name="before-you-begin"></a>开始之前

我们建议安装[我的应用安全登录扩展](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)。 此浏览器扩展，可以轻松地解决问题的单一登录到收集的 SAML 请求和所需的 SAML 响应信息。 如果无法安装该扩展，本文介绍了在已安装和未安装该扩展的情况下如何解决问题。

若要下载并安装“我的应用安全登录扩展”，请使用以下链接之一。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>测试基于 SAML 的单一登录

若要测试基于 SAML 的单一登录 Azure AD 之间和目标应用程序：

1. 以全局管理员或者有权管理应用程序的其他管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧边栏选项卡，选择**Azure Active Directory**，然后选择**企业应用程序**。 
1. 从企业应用程序列表中，选择应用程序为想要测试单一登录，然后在左侧选择的选项从**单一登录**。
1. 若要打开的基于 SAML 的单一登录测试体验，请转到**单一登录测试**（步骤 5）。 如果**测试**按钮将灰显，则需要首次填写，然后保存所需的属性**基本 SAML 配置**部分。
1. 在“测试单一登录”边栏选项卡中，使用企业凭据登录到目标应用程序。  可以当前用户或其他用户的身份登录。 如果以其他用户的身份登录，系统会提示进行身份验证。

    ![“测试 SAML”页](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


如果已成功登录，则表示通过了测试。 在这种情况下，Azure AD 已向应用程序颁发了 SAML 响应令牌。 应用程序已使用该 SAML 令牌成功将你登录。

如果公司登录页或应用程序页上出现错误，请参考后续某个部分来解决该错误。


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>解决公司登录页上的登录错误

当您尝试登录时，你可能会看到错误你公司的登录页上类似于下面的示例。

![登录错误](./media/howto-v1-debug-saml-sso-issues/error.png)

若要调试此错误，需要获取错误消息和 SAML 请求。 “我的应用安全登录扩展”会自动收集此信息，并在 Azure AD 中显示解决方法指导。 

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>若要解决登录错误后与我的应用安全登录扩展安装

1. 出现错误时，该扩展将你重定向到 Azure AD**单一登录测试**边栏选项卡。 
1. 上**测试单一登录**边栏选项卡，选择**下载 SAML 请求**。 
1. 根据 SAML 请求中的错误和值，应会显示具体的解决方法指导。
1. 你将看到**修复此错误**按钮以自动更新来解决此问题的 Azure AD 中的配置。 如果看不到此按钮，然后登录问题不是由于 Azure AD 上配置错误。

如果没有解决方法，用于登录错误，我们建议使用反馈文本框中时通知我们。

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>若要解决此错误，而无需安装我的应用安全登录扩展

1. 复制页面右下角的错误消息。 错误消息中包含：
    - CorrelationID 和 Timestamp。 创建 Microsoft 支持案例时，这些值非常重要，因为它们可以帮助工程师识别问题，并提供问题的准确解决方法。
    - 指明问题根本原因的陈述。
1. 返回 Azure AD 并找到“测试单一登录”边栏选项卡。 
1. 在“获取解决方法指导”上面的文本框中，粘贴该错误消息。 
1. 单击“获取解决方法指导”显示解决问题的步骤。  该指导可能需要 SAML 请求或 SAML 响应中的信息。 如果您不使用我的应用安全登录扩展，则可能需要一种工具如[Fiddler](https://www.telerik.com/fiddler)检索 SAML 请求和响应。
1. 验证 SAML 请求中的目标对应于 SAML 单一登录服务 URL 从 Azure AD 获取。
1. 验证 SAML 请求中的颁发者已配置 Azure AD 中应用程序相同的标识符。 Azure AD 使用颁发者在目录中查找应用程序。
1. 验证 AssertionConsumerServiceURL 是应用程序能够接收来自 Azure AD 的 SAML 令牌。 您可以在 Azure AD 中配置此值，但并不总是这样如果它是 SAML 请求的一部分。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>解决应用程序页上的登录错误

成功登录后，应用程序页上也仍有可能会出现错误。 当 Azure AD 向应用程序颁发了令牌，但应用程序未接受响应时，将发生此错误。   

若要解决该错误，请执行以下步骤：

1. 如果应用程序在 Azure AD 库中，验证已按照用于与 Azure AD 集成应用程序的所有步骤。 若要查找应用程序的集成说明，请参阅 [SaaS 应用程序集成教程列表](../saas-apps/tutorial-list.md)。
1. 检索 SAML 响应。
    - 如果已安装“我的应用安全登录扩展”，请在“测试单一登录”边栏选项卡中单击“下载 SAML 响应”。  
    - 如果未安装该扩展，请使用某种工具（例如 [Fiddler](https://www.telerik.com/fiddler)）来检索 SAML 响应。 
1. 请注意 SAML 响应令牌中的以下元素：
   - NameID 用户唯一标识符值和格式
   - 在令牌中颁发的声明
   - 用于令牌签名的证书。 

     有关 SAML 响应的详细信息，请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)。

1. 现在，已经学习了 SAML 响应，请参阅[登录后的应用程序的页面上的错误](../manage-apps/application-sign-in-problem-application-error.md)有关如何解决该问题的指南。 
1. 如果您仍然无法成功登录，你可以要求应用程序供应商 SAML 响应中缺少了什么。


## <a name="next-steps"></a>后续步骤

现在，实现单一登录使用到你的应用程序，你可以[自动用户预配和取消预配到 SaaS 应用程序](../manage-apps/user-provisioning.md)或[开始使用条件性访问](../conditional-access/app-based-conditional-access.md)。
