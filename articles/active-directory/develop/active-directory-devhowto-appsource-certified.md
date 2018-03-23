---
title: 如何使 AppSource 通过 Azure Active Directory 的认证 | Microsoft Docs
description: 详细说明如何使应用程序 AppSource 通过 Azure Active Directory 的认证。
services: active-directory
documentationcenter: ''
author: andretms
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5601ad80e271364fec519cf34bcdc2f650f3bb92
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2018
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>如何使 AppSource 通过 Azure Active Directory 的认证
[Microsoft AppSource](https://appsource.microsoft.com/) 是一个可供业务用户发现、尝试和管理业务线 SaaS 应用程序（独立 SaaS 和现有 Microsoft SaaS 产品的加载项）的目的地。

若要在 AppSource 上列出独立 SaaS 应用程序，你的应用程序必须接受来自拥有 Azure Active Directory 的任何公司或组织的工作帐户的单一登录。 单一登录过程必须使用 [OpenID Connect](./active-directory-protocols-openid-connect-code.md) 或 [OAuth 2.0](./active-directory-protocols-oauth-code.md) 协议。 AppSource 认证不接受 SAML 集成。

## <a name="guides-and-code-samples"></a>指南和代码示例
如果要了解如何使用 OpenID 连接将应用程序与 Azure Active Directory 集成，请遵循 [Azure Active Directory 开发人员指南](./active-directory-developers-guide.md#get-started "Azure AD 开发人员入门")中的指南和代码示例。

## <a name="multi-tenant-applications"></a>多租户应用程序

可接受来自拥有 Azure Active Directory 的任何公司或组织的用户的登录，而无需单独实例、配置或部署的应用程序，被称为多租户应用程序。 AppSource 建议应用程序实施多租户来启用单击免费试用体验。

若要在应用程序上启用多租户，请执行以下操作：
- 在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)中将应用程序注册信息上的 `Multi-Tenanted` 属性设置为 `Yes`（默认情况下，在 Azure 门户中创建的应用程序将配置为单租户）
- 更新代码，将请求发送到“`common`”终结点（将终结点从 https://login.microsoftonline.com/{yourtenant} 更新到 https://login.microsoftonline.com/common）
- 对于某些平台（如 ASP.NET），还需更新代码以接受多个证书颁发者

有关多租户的详细信息，请参阅：[如何使用多租户应用程序模式登录任意 Azure Active Directory (AD) 用户](./active-directory-devhowto-multi-tenant-overview.md)。

### <a name="single-tenant-applications"></a>单租户应用程序
如果应用程序仅接受来自定义的 Azure Active Directory 实例的用户的登录，这样的应用程序便称为单租户应用程序。 将每个用户作为来宾帐户添加到应用程序注册的 Azure Active Directory 实例后，外部用户（包括来自其他组织的工作或学校帐户，或个人帐户）可以登录到单租户应用程序。 可通过 [Azure AD B2B 协作](../active-directory-b2b-what-is-azure-ad-b2b.md)将用户作为来宾帐户添加到 Azure Active Directory - 此操作可[以编程方式](../active-directory-b2b-code-samples.md)完成。 在将用户作为来宾帐户添加到 Azure Active Directory 时，会向用户发送一封邀请电子邮件，用户必须单击邀请电子邮件中的链接来接受邀请。 不需要发送给邀请组织（也属于合作伙伴组织一员）中另一用户的邀请，便可接受邀请以登录。

单租户应用程序可启用“与我联系”体验，但若要启用 AppSource 建议的单击/免费试用体验，则请改为在应用程序上启用多租户。


## <a name="appsource-trial-experiences"></a>AppSource 试用体验

### <a name="free-trial-customer-led-trial-experience"></a>免费试用（客户导向型试用体验） 
客户导向型试用是 AppSource 所推荐的体验，因为它提供对应用程序的单击访问。 以下内容将具体说明此体验：<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>用户在 AppSource 网站中查找你的应用程序</li><li>选择“免费试用”选项</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource 将用户重定向到你网站中的 URL</li><li>网站将自动开始<i>单一登录</i>过程（页面加载中）</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>用户被重定向到 Microsoft 登录页</li><li>用户提供凭据以登录</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>用户对你的应用程序授权</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>登录完成，且用户被重定向回你的网站</li><li>用户开始进行免费试用</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>与我联系（合作伙伴导向型试用体验）
当需要执行手动或长期操作来预配用户/公司（例如，应用程序需要预配虚拟机、数据库实例或耗费大量时间才可完成的操作）时，可使用合作伙伴试用体验。 在这种情况下，当用户选择“请求试用”按钮并填写表单后，AppSource 将向你发送用户的联系信息。 收到此信息后，需预配环境并就如何访问试用体验向用户发送说明：<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>用户在 AppSource 网站中查找你的应用程序</li><li>选择“与我联系”选项</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>在表单中填写联系信息</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>你接收到用户信息</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>设置环境</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>就试用信息与用户联系</td>
        </tr>
        </table><br/><br/>
        <ul><li>你接收到用户信息并设置试用实例</li><li>向用户发送用于访问你的应用程序的超链接</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>用户访问你的应用程序并完成单一登录过程</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>用户对你的应用程序授权</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>登录完成，且用户被重定向回你的网站</li><li>用户开始进行免费试用</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>详细信息
有关 AppSource 试用体验的详细信息，请参阅[此视频](https://aka.ms/trialexperienceforwebapps)。 
 
## <a name="next-steps"></a>后续步骤

- 有关构建支持 Azure Active Directory 登录的应用程序的详细信息，请参阅 [Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- 有关如何在 AppSource 中列出你的 SaaS 应用程序的信息，请参阅 [AppSource 合作伙伴信息](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>获取支持
对于 Azure Active Directory 集成，我们使用 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) 以及社区来提供支持。 

强烈建议先在 Stack Overflow 上提问并浏览现有问题，查看是否已有人提出过相同疑问。 请务必将提问或评论用 [`[azure-active-directory]` 和 `[appsource]`](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) 标记。

使用以下评论部分提供反馈，帮助我们改进内容。

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->