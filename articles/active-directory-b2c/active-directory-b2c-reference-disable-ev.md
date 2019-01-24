---
title: 在 Azure Active Directory B2C 中，在使用者注册期间禁用电子邮件验证 | Microsoft Docs
description: 本主题演示在使用者在 Azure Active Directory B2C 中注册期间如何禁用电子邮件验证。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7bfbf4da0b5af447eaa88f9c9187a67a7998d0e4
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842879"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>使用者在 Azure Active Directory B2C 中注册期间禁用电子邮件验证 
启用后，Azure Active Directory (Azure AD) B2C 让使用者能够通过提供电子邮件地址和创建本地帐户来注册应用程序。 Azure AD B2C 通过要求使用者在注册过程中验证电子邮件地址来确保电子邮件地址有效。 它还阻止恶意的自动化过程生成应用程序的假帐户。

某些应用程序开发人员愿意在注册过程中跳过电子邮件验证，而让使用者在以后验证电子邮件地址。 要支持此功能，可将 Azure AD B2C 配置为禁用电子邮件验证。 这样做可创建更流畅的注册过程，并使开发人员可以灵活地将已验证电子邮件地址的使用者与尚未验证电子邮件地址的使用者区分开来。

默认情况下，注册用户流已打开电子邮件验证。 使用以下步骤可将其关闭：

1. 单击“用户流”。
2. 单击用户流（例如，“B2C_1_SiUp”）将其打开。 
3. 单击“页面布局”。
4. 单击“本地帐户注册页”。
5. 单击“用户属性”部分下“名称”列中的“电子邮件地址”。
6. 在“要求验证”下选择“否”。
7. 单击边栏选项卡顶部的“保存”。 大功告成！

> [!NOTE]
> 在注册过程中禁用电子邮件验证可能会导致垃圾邮件。 如果禁用了默认的电子邮件验证，我们建议添加自己的验证系统。
> 
>