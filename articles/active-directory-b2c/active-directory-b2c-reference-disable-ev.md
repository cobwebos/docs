---
title: "Azure Active Directory B2C：在使用者注册期间禁用电子邮件验证 | Microsoft 文档"
description: "本主题演示在使用者在 Azure Active Directory B2C 中注册期间如何禁用电子邮件验证"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 433f32b8-96d2-4113-aa82-efcf42fa9827
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/06/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: cedc7d3c16dd8acac2700f461305d28a8ba6c802
ms.openlocfilehash: 5ab121e536479ef5584b222456e127d5bb12e24d


---
# <a name="azure-active-directory-b2c-disable-email-verification-during-consumer-sign-up"></a>Azure Active Directory B2C：在使用者注册期间禁用电子邮件验证
启用后，Azure Active Directory (Azure AD) B2C 让使用者能够通过提供电子邮件地址和创建本地帐户来注册应用程序。 Azure AD B2C 通过要求使用者在注册过程中验证电子邮件地址来确保电子邮件地址有效。 它还阻止恶意的自动化过程生成应用程序的假帐户。

某些应用程序开发人员愿意在注册过程中跳过电子邮件验证，而让使用者在以后验证电子邮件地址。 若要支持此功能，可将 Azure AD B2C 配置为禁用电子邮件验证。 这样做可创建更流畅的注册过程，并使开发人员可以灵活地将已验证电子邮件地址的使用者与尚未验证电子邮件地址的使用者区分开来。

默认情况下，注册策略已打开电子邮件验证。 使用以下步骤可将其关闭：

1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“注册策略”或“注册或登录策略”，具体取决于你对注册进行的配置。
3. 单击你的策略（例如，“B2C_1_SiUp”）将其打开。 单击边栏选项卡顶部的“编辑”。
4. 单击“页面 UI 自定义”。
5. 单击“本地帐户注册页”。
6. 单击“注册属性”部分下“名称”列中的“电子邮件地址”。
7. 将“要求验证”选项切换到“否”。
8. 单击底部的“确定”，直到到达“编辑策略”边栏选项卡。
9. 单击边栏选项卡顶部的“保存”。 大功告成！

> [!NOTE]
> 在注册过程中禁用电子邮件验证可能会导致垃圾邮件。 如果你禁用了默认的电子邮件验证，我们建议添加你自己的验证系统。
> 
> 

我们始终乐于接受反馈和建议！ 如果在完成本主题的过程中遇到任何难题，或者在改进此内容方面有任何建议，请在页面底部提供反馈，我们将不胜感激。 对于功能请求，请将其添加到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。


<!--HONumber=Feb17_HO2-->


