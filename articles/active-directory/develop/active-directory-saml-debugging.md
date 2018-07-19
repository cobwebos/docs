---
title: 调试基于 SAML 的单一登录 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录。
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 0192b2d89b5e97c7f23b5394106853ddf83ddaf4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044003"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录

了解如何在 Azure Active Directory (Azure AD) 中查找和解决支持[安全断言标记语言 (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) 的应用程序的[单一登录](../manage-apps/what-is-single-sign-on.md)问题。 

## <a name="before-you-begin"></a>开始之前
我们建议安装[我的应用安全登录扩展](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension)。 使用此浏览器扩展可以轻松收集解决单一登录问题时所需的 SAML 请求和 SAML 响应信息。 如果无法安装该扩展，本文介绍了在已安装和未安装该扩展的情况下如何解决问题。

若要下载并安装“我的应用安全登录扩展”，请使用以下链接之一。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>测试基于 SAML 的单一登录

在 AAD 与目标应用程序之间测试基于 SAML 的单一登录：

1.  以全局管理员或者有权管理应用程序的其他管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2.  在左侧边栏选项卡中，依次单击“Azure Active Directory”、“企业应用程序”。 
3.  在“企业应用程序”列表中，单击要对其测试单一登录的应用程序，然后在左侧的选项中单击“单一登录”。
4.  若要打开基于 SAML 的单一登录测试体验，请在“域和 URL”部分单击“测试 SAML 设置”。 如果“测试 SAML 设置”按钮灰显，则需要先填写并保存所需的属性。
5.  在“测试单一登录”边栏选项卡中，使用企业凭据登录到目标应用程序。 可以当前用户或其他用户的身份登录。 如果以其他用户的身份登录，系统会提示进行身份验证。

    ![“测试 SAML”页](media/active-directory-saml-debugging/testing.png)


如果已成功登录，则表示通过了测试。 在这种情况下，Azure AD 已向应用程序颁发了 SAML 响应令牌。 应用程序已使用该 SAML 令牌成功将你登录。

如果公司登录页或应用程序页上出现错误，请参考后续某个部分来解决该错误。


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>解决公司登录页上的登录错误

尝试登录时，公司登录页上可能会出现错误。 

![登录错误](media/active-directory-saml-debugging/error.png)

若要调试此错误，需要获取错误消息和 SAML 请求。 “我的应用安全登录扩展”会自动收集此信息，并在 Azure AD 中显示解决方法指导。 

在安装了“我的应用安全登录扩展”的情况下解决登录错误：

1.  出现错误时，该扩展会将你重定向回到 Azure AD 的“测试单一登录”边栏选项卡。 
2.  在“测试单一登录”边栏选项卡上，单击“下载 SAML 请求”。 
3.  根据 SAML 请求中的错误和值，应会显示具体的解决方法指导。 查看该指导。

在未安装“我的应用安全登录扩展”的情况下解决错误：

1. 复制页面右下角的错误消息。 错误消息中包含：
    - CorrelationID 和 Timestamp。 创建 Microsoft 支持案例时，这些值非常重要，因为它们可以帮助工程师识别问题，并提供问题的准确解决方法。
    - 指明问题根本原因的陈述。
2.  返回 Azure AD 并找到“测试单一登录”边栏选项卡。
3.  在“获取解决方法指导”上面的文本框中，粘贴该错误消息。
3.  单击“获取解决方法指导”显示解决问题的步骤。 该指导可能需要 SAML 请求或 SAML 响应中的信息。 如果未使用“我的应用安全登录扩展”，可能需要使用某种工具（例如 [Fiddler](http://www.telerik.com/fiddler)）来检索 SAML 请求和响应。
4.  验证 SAML 请求中的目标是否对应于从 Azure Active Directory 获取的 SAML 单一登录服务 URL。
5.  验证 SAML 请求中的颁发者是否与在 Azure Active Directory 中为应用程序配置的标识符相同。 Azure AD 使用颁发者在目录中查找应用程序。
6.  验证应用程序是否预期在 AssertionConsumerServiceURL 中从 Azure Active Directory 接收 SAML 令牌。 可以在 Azure Active Directory 中配置此值，但如果此值是 SAML 请求的一部分，则不一定要这样做。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>解决应用程序页上的登录错误

成功登录后，应用程序页上也仍有可能会出现错误。 当 Azure AD 向应用程序颁发了令牌，但应用程序未接受响应时，将发生此错误。   

解决该错误：

1. 如果应用程序在 Azure AD 库中，请验证是否已遵循将应用程序与 Azure AD 集成的所有步骤。 若要查找应用程序的集成说明，请参阅 [SaaS 应用程序集成教程列表](../saas-apps/tutorial-list.md)。
2. 检索 SAML 响应。
    - 如果已安装“我的应用安全登录扩展”，请在“测试单一登录”边栏选项卡中单击“下载 SAML 响应”。
    - 如果未安装该扩展，请使用某种工具（例如 [Fiddler](http://www.telerik.com/fiddler)）来检索 SAML 响应。 
3. 请注意 SAML 响应令牌中的以下元素：
    - NameID 用户唯一标识符值和格式
    - 在令牌中颁发的声明
    - 用于令牌签名的证书。 有关如何查看 SAML 响应的信息，请参阅[单一登录 SAML 协议](active-directory-single-sign-on-protocol-reference.md)。
4. 有关 SAML 响应的详细信息，请参阅[单一登录 SAML 协议](active-directory-single-sign-on-protocol-reference.md)。
5. 查看 SAML 响应后，请参阅[登录后应用程序页上出现错误](../application-sign-in-problem-application-error.md)来获取有关解决问题的指导。 
6. 如果仍然无法成功登录，可以咨询应用程序供应商，以确定 SAML 响应中缺少哪些信息。


## <a name="next-steps"></a>后续步骤
在应用程序中正常执行单一登录后，可以[在 SaaS 应用程序中自动完成用户预配和取消预配](../active-directory-saas-app-provisioning.md)，或[开始使用条件访问](../active-directory-conditional-access-azure-portal-get-started.md)。


