---
title: "帐户预配通知 | Microsoft Docs"
description: "了解如何通过启用帐户预配通知，确保获得与用户预配相关的、需要注意的问题的通知。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: a637aac7-f06b-48ef-a66d-639835a8edec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: e15f3740fbf7cd97002b0b7efda34e968db4fe3c
ms.lasthandoff: 12/29/2016


---
# <a name="account-provisioning-notifications"></a>帐户预配通知
使用用户预配可以自动完成在第三方 SaaS 应用程序中管理用户这一过程。 <br>
虽然这是一个自动化的过程，但也时不时需要与此过程交互。 <br>
例如，如果已配置为与第三方 SaaS 应用程序交换数据的帐户的密码过期，则需进行交互。 

通过启用帐户预配通知，可确保获得与用户预配相关的、需要注意的问题的通知。

在为第三方 SaaS 应用程序进行用户预配配置的过程中，可以激活或停用帐户预配通知。

![用户预配][1] 

若要激活帐户预配通知，请在“确认”对话框页上选中相关复选框，然后键入收件人的电子邮件别名。

![帐户预配通知][2]

可以输入通讯组列表作为收件人；但必须注意的是，通知电子邮件包含仅供 Azure AD 管理员访问的报告链接。

如果启用了帐户预配通知，则会收到与用户预配相关的关键问题的电子邮件。 不过，为了避免电子邮件过载，对于每个启用了通知电子邮件的 SaaS 应用程序，用户一天只会收到一封通知电子邮件。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [在 SaaS 应用中自动预配和取消预配用户](active-directory-saas-app-provisioning.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

