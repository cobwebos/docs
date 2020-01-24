---
title: 调试基于 SAML 的单一登录 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: 630d178a4f1a802d5c98b570544822e4f4013a5a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697007"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录

了解如何在 Azure Active Directory (Azure AD) 中查找和解决支持[安全断言标记语言 (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 的应用程序的[单一登录](../manage-apps/what-is-single-sign-on.md)问题。 

## <a name="before-you-begin"></a>开始之前

我们建议安装[我的应用安全登录扩展](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)。 利用此浏览器扩展，可以轻松收集解决单一登录问题所需的 SAML 请求和 SAML 响应信息。 如果无法安装该扩展，本文介绍了在已安装和未安装该扩展的情况下如何解决问题。

若要下载并安装“我的应用安全登录扩展”，请使用以下链接之一。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>测试基于 SAML 的单一登录

若要在 Azure AD 与目标应用程序之间测试基于 SAML 的单一登录：

1. 以全局管理员或者有权管理应用程序的其他管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧边栏选项卡中，选择 " **Azure Active Directory**"，然后选择 "**企业应用程序**"。 
1. 从企业应用程序列表中，选择要为其测试单一登录的应用程序，然后从左侧的选项中选择 "**单一登录**"。
1. 若要打开基于 SAML 的单一登录测试体验，请参阅**测试单一登录**（步骤5）。 如果 "**测试**" 按钮灰显，则需要首先在 "**基本 SAML 配置**" 部分中填写并保存所需的属性。
1. 在“测试单一登录”边栏选项卡中，使用企业凭据登录到目标应用程序。 可以当前用户或其他用户的身份登录。 如果以其他用户的身份登录，系统会提示进行身份验证。

    ![显示 "测试 SAML SSO" 页的屏幕截图](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

如果已成功登录，则表示通过了测试。 在这种情况下，Azure AD 已向应用程序颁发了 SAML 响应令牌。 应用程序已使用该 SAML 令牌成功将你登录。

如果公司登录页或应用程序页上出现错误，请参考后续某个部分来解决该错误。

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>解决公司登录页上的登录错误

尝试登录时，可能会在公司登录页上看到类似于以下示例的错误。

![示例在公司登录页中显示错误](./media/howto-v1-debug-saml-sso-issues/error.png)

若要调试此错误，需要获取错误消息和 SAML 请求。 “我的应用安全登录扩展”会自动收集此信息，并在 Azure AD 中显示解决方法指导。

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>若要解决安装了 "我的应用" 安全登录扩展的登录错误

1. 出现错误时，扩展会将你重定向回 Azure AD**测试单一登录**"边栏选项卡。
1. 在 "**测试单一登录**" 边栏选项卡上，选择 **"下载 SAML 请求"** 。
1. 根据 SAML 请求中的错误和值，应会显示具体的解决方法指导。
1. 你将看到 "**修复它**" 按钮，用于在 Azure AD 中自动更新配置以解决此问题。 如果看不到此按钮，则表明登录问题不是由于 Azure AD 上的配置错误引起的。

如果没有为登录错误提供任何解决方案，我们建议使用 "反馈" 文本框来通知我们。

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>若要解决此错误而不安装 "我的应用" 安全登录扩展

1. 复制页面右下角的错误消息。 错误消息中包含：
    - CorrelationID 和 Timestamp。 创建 Microsoft 支持案例时，这些值非常重要，因为它们可以帮助工程师识别问题，并提供问题的准确解决方法。
    - 指明问题根本原因的陈述。
1. 返回 Azure AD 并找到“测试单一登录”边栏选项卡。
1. 在“获取解决方法指导”上面的文本框中，粘贴该错误消息。
1. 单击“获取解决方法指导”显示解决问题的步骤。 该指导可能需要 SAML 请求或 SAML 响应中的信息。 如果使用的是 "我的应用" 安全登录扩展，则可能需要[Fiddler](https://www.telerik.com/fiddler)等工具来检索 SAML 请求和响应。
1. 验证 SAML 请求中的目标是否对应于从 Azure AD 获取的 SAML 单一登录服务 URL。
1. 验证 SAML 请求中的颁发者是否为 Azure AD 中为应用程序配置的同一标识符。 Azure AD 使用颁发者在目录中查找应用程序。
1. 验证 AssertionConsumerServiceURL 是应用程序预期从 Azure AD 接收 SAML 令牌的位置。 可以在 Azure AD 中配置此值，但如果它是 SAML 请求的一部分，则不是必需的。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>解决应用程序页上的登录错误

成功登录后，应用程序页上也仍有可能会出现错误。 当 Azure AD 向应用程序颁发了令牌，但应用程序未接受响应时，将发生此错误。

若要解决该错误，请执行以下步骤：

1. 如果应用程序位于 Azure AD 库中，请验证是否已遵循将应用程序与 Azure AD 集成的所有步骤。 若要查找应用程序的集成说明，请参阅 [SaaS 应用程序集成教程列表](../saas-apps/tutorial-list.md)。
1. 检索 SAML 响应。
    - 如果已安装“我的应用安全登录扩展”，请在“测试单一登录”边栏选项卡中单击“下载 SAML 响应”。
    - 如果未安装该扩展，请使用某种工具（例如 [Fiddler](https://www.telerik.com/fiddler)）来检索 SAML 响应。
1. 请注意 SAML 响应令牌中的以下元素：
   - NameID 用户唯一标识符值和格式
   - 在令牌中颁发的声明
   - 用于令牌签名的证书。

     有关 SAML 响应的详细信息，请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)。

1. 查看 SAML 响应后，请在[登录后参阅应用程序页面上的 "错误"](../manage-apps/application-sign-in-problem-application-error.md) ，以获取有关如何解决此问题的指南。 
1. 如果你仍然无法成功登录，则可以向应用程序供应商询问 SAML 响应中缺少的内容。

## <a name="next-steps"></a>后续步骤

现在，单一登录正在使用应用程序，你可以[自动执行用户预配和取消预配到 SaaS 应用程序](../manage-apps/user-provisioning.md)或[开始使用条件性访问](../conditional-access/app-based-conditional-access.md)。
