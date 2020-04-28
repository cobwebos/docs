---
title: 添加组织的隐私信息 - Azure Active Directory | Microsoft Docs
description: 有关如何在 Azure Active Directory“属性”区域中添加组织的隐私信息的顺明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80876178"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>使用 Azure Active Directory 添加组织的隐私信息
本文介绍了租户管理员如何通过 Azure 门户将与隐私相关的信息添加到组织的 Azure Active Directory （Azure AD）租户。

我们强烈建议你同时添加全球隐私联系人和组织的隐私声明，以便内部员工和外部来宾能够查看你的策略。 由于隐私声明是为每个企业唯一创建和定制的，我们强烈建议你与律师联系以获取帮助。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>在 Azure AD 上添加隐私信息
可以在 Azure AD 的“属性”区域中添加组织的隐私信息。 

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>访问“属性”区域并添加隐私信息

1.    以租户管理员身份登录到 Azure 门户。

2.    在左侧导航栏中，选择“Azure Active Directory”  ，然后选择“属性”  。

    此时会显示“属性”区域。 

    ![突出显示隐私信息区域的 Azure AD 属性区域](media/active-directory-properties-area/properties-area.png)

3.    为员工添加隐私信息：

    - **技术联系人。** 键入组织中与其联系以获得技术支持的人员的电子邮件地址。
    
    - **全局隐私联系人。** 键入与其联系以获得个人数据隐私查询的人员的电子邮件地址。 如果存在数据安全漏洞，此人同时也是 Microsoft 联系人。 如果此处未列出任何人，Microsoft 将联系全局管理员。

    - **隐私声明 URL。** 键入组织文档的链接，该链接描述组织如何处理内部和外部来宾的数据隐私。

        >[!Important]
        >如果未包括你自己的隐私声明或隐私联系人，你的外部来宾将在 "**审阅权限**" 框中看到文本， ** <其中显示了_你的组织名称_> 未向你提供要查看的条款的链接**。 例如，来宾用户将在收到通过 B2B 协作访问组织的邀请时看到此消息。

        ![带有消息的 B2B 协作审阅权限框](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    选择“保存”。 

## <a name="next-steps"></a>后续步骤
- [Azure Active Directory B2B 协作邀请兑换](../b2b/redemption-experience.md)
- [在 Azure Active Directory 中添加或更改用户的个人资料信息](active-directory-users-profile-azure-portal.md)
