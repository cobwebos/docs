---
title: "在 Azure Active Directory 中管理密码 | Microsoft Docs"
description: "如何在 Azure Active Directory 中管理密码。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a7679724-0ed5-4973-92e2-bd1285a6ef93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: fee05b9cfb176714e3313ea2e969958bb4cb19f8


---
# <a name="manage-passwords-in-azure-active-directory"></a>在 Azure Active Directory 中管理密码
如果你是管理员，可以通过 Azure 经典门户中的 Azure Active Directory (Azure AD) 重置用户密码。 单击目录名称，在“用户”页上单击相应的用户名，然后在门户底部单击“重置密码”。

本主题的余下部分将介绍 Azure AD 支持的整套密码管理功能，包括：

* **自助密码**更改允许最终用户或管理员更改过期或未过期的密码，而无需请求管理员或支持人员提供支持。
* **自助密码**重置允许最终用户或管理员自动重置密码，而无需请求管理员或支持人员提供支持。 自助密码重置功能需要 Azure AD 高级或基本版。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
* **管理员启动的密码重置**：管理员可以通过 Azure 经典门户重置某个最终用户的密码或其他管理员的密码。
* **密码管理活动报告**允许管理员深入了解发生在其组织中的密码重置和注册活动。
* **密码写回**允许从云管理本地密码，因此，上述所有方案都可以由联合用户或密码同步用户本人或其代表来执行。 密码写回功能需要 Azure AD 高级版。 有关详细信息，请参阅 [Azure Active Directory Premium 入门](active-directory-get-started-premium.md)。

> [!NOTE]
> Azure AD 高级版适用于使用世界范围的 Azure AD 实例的中国客户。 由中国的 21Vianet 运营的 Microsoft Azure 服务目前不支持 Azure AD 高级版。 有关详细信息，请在 [Azure Active Directory 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。
>
>

使用以下链接可跳转至你最感兴趣的文档：

* [概述：Azure AD 中的密码管理](active-directory-passwords-how-it-works.md)
* [Azure AD 中的自助密码重置：如何启用、配置和测试自助密码重置](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
* [Azure AD 中的自助密码重置：如何根据你的需要自定义密码重置](active-directory-passwords-customize.md)
* [Azure AD 中的自助密码重置：部署和管理最佳实践](active-directory-passwords-best-practices.md)
* [Azure AD 中的密码管理报告：如何查看租户中的密码管理活动](active-directory-passwords-get-insights.md)
* [密码写回：如何配置 Azure AD 以管理本地密码](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
* [Azure AD 密码管理故障排除](active-directory-passwords-troubleshoot.md)
* [Azure AD 密码管理常见问题](active-directory-passwords-faq.md)

## <a name="next-steps"></a>后续步骤
* [管理 Azure AD](active-directory-administer.md)
* [在 Azure AD 中创建或编辑用户](active-directory-create-users.md)
* [在 Azure AD 中管理组](active-directory-manage-groups.md)



<!--HONumber=Feb17_HO2-->


