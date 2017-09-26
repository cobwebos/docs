---
title: "在 Azure Active Directory 中为预先集成的应用自定义 SAML 令牌中颁发的声明 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中为预先集成的应用自定义 SAML 令牌中颁发的声明"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 6d232759630fcc567788a8326b566b659f89d17a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>在 Azure Active Directory 中为预先集成的应用自定义 SAML 令牌中颁发的声明
Azure Active Directory 目前支持 Azure AD 应用库中数千个预先集成的应用程序，其中有 360 个以上的应用程序支持使用 SAML 2.0 协议的单一登录。 当用户通过 Azure AD 使用 SAML 对应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌将用户登录，而不是提示输入用户名和密码。 这些 SAML 令牌包含有关用户的信息片段（称为“声明”）。

在标识交谈中，“声明”是标识提供者在为某个用户颁发的令牌中陈述的有关该用户的信息。 在 [SAML 令牌](http://en.wikipedia.org/wiki/SAML_2.0)中，此数据通常包含在 SAML 属性语句中。 用户的唯一 ID（也称为名称标识符）通常显示在 SAML 主题中。

默认情况下，Azure Active Directory 向应用程序颁发 SAML 令牌，其中包含 NameIdentifier 声明，以及用户在 Azure AD 中的用户名值（AKA 用户主体名称）。 此值可唯一地标识用户。 SAML 令牌还含有其他声明，其中包含用户的电子邮件地址、名字和姓氏。

若要查看或编辑 SAML 令牌中颁发给应用程序的声明，请在 Azure 门户中打开应用程序。 然后在应用程序的“用户属性”部分中，选择“查看和编辑所有其他用户属性”复选框。

![用户属性部分][1]

有两个可能的原因使你可能需要编辑 SAML 令牌中颁发的声明：
* 应用程序已编写为需要一组不同的声明 URI 或声明值。
* 应用程序的部署方式需要 NameIdentifier 声明为存储在 Azure Active Directory 中的用户名（AKA 用户主体名称）以外的其他内容。

可以编辑任何默认声明值。 在 SAML 令牌属性表中选择声明行。 这会打开“编辑属性”部分，可在其中编辑声明名称、值以及与声明关联的命名空间。

![编辑用户属性][2]

可通过单击“...”图标打开上下文菜单，然后使用该菜单删除声明（而不是 NameIdentifier）。  还可使用“添加属性”按钮添加新声明。

![编辑用户属性][3]

## <a name="editing-the-nameidentifier-claim"></a>编辑 NameIdentifier 声明
若要解决使用不同用户名部署应用程序的问题，请单击“用户属性”部分的“用户标识符”下拉列表。 此操作提供包含多个不同选项的对话框：

![编辑用户属性][4]

在下拉列表中，选择“user.mail”，将 NameIdentifier 声明设置为目录中用户的电子邮件地址。 或者，选择“user.onpremisessamaccountname”，设置为已从本地 Azure AD 同步的用户 SAM 帐户名。

还可以使用特殊的 ExtractMailPrefix() 函数删除电子邮件地址、SAM 帐户名或用户主体名称中的域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。

![编辑用户属性][5]

现在还可使用 join() 函数，通过用户标识符值加入已验证域。 在“用户标识符”中选择 join() 函数时，首先选择用户标识符（如电子邮件地址或用户主体名称），然后在第二个下拉列表中选择已验证的域。 如果选择已验证域的电子邮件地址，则 Azure AD 从 joe_smith@contoso.com 的第一个值 joe_smith 中提取用户名，并向该用户名追加 contoso.onmicrosoft.com。请参阅以下示例：

![编辑用户属性][6]

## <a name="adding-claims"></a>添加声明
添加声明时，可以指定属性名称（根据 SAML 规范，并不严格需要遵循 URI 模式）。 将值设置为目录中存储的任何用户属性。

![添加用户属性][7]

例如，需要以声明的形式发送用户在其组织中所属的部门（例如，Sales）。 输入应用程序所需的声明名称，并选择“user.department”作为值。

> [!NOTE]
> 如果指定的用户没有针对选定属性存储的值，则不会在令牌中颁发该声明。

> [!TIP]
> 仅在使用 [Azure AD Connect 工具](../active-directory-aadconnect.md)从本地 Active Directory 同步用户数据时，才支持“user.onpremisesecurityidentifier”和“user.onpremisesamaccountname”。

## <a name="restricted-claims"></a>受限声明

SAML 中有一些受限声明。 如果添加此类声明，Azure AD 不会发送它们。 以下是 SAML 受限声明集：

    | 声明类型 (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>后续步骤
* [有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)
* [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](../active-directory-saas-custom-apps.md)
* [排查基于 SAML 的单一登录问题](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
