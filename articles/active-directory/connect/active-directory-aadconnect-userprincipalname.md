---
title: Azure AD UserPrincipalName 填充
description: 以下文档介绍了如何填充 UserPrincipalName 属性。
author: billmath
ms.component: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 6b3fddcdf6ff9c35d5932b9b83da02f60f9e081e
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064257"
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD UserPrincipalName 填充

本文介绍如何在 Azure Active Directory (Azure AD) 中填充 UserPrincipalName 属性。
UserPrincipalName 属性值是用户帐户的 Azure AD 用户名。

## <a name="upn-terminology"></a>UPN 术语
本文使用了以下术语：

|术语|说明|
|-----|-----|
|初始域|Azure AD 租户中的默认域 (onmicrosoft.com)。 例如 contoso.onmicrosoft.com。|
|Microsoft 联机电子邮件路由地址 (MOERA)|Azure AD 基于 Azure AD MailNickName 属性和 &lt;MailNickName&gt;&#64;&lt;初始域&gt; 形式的 Azure AD 初始域计算 MOERA。|
|本地 mailNickName 属性|Active Directory 中的一个属性，其值表示 Exchange 组织中某个用户的别名。|
|本地 mail 属性|Active Directory 中的一个属性，其值表示某个用户的电子邮件地址|
|主要 SMTP 地址|Exchange 收件人对象的主要电子邮件地址。 例如，SMTP:user\@contoso.com。|
|备用登录 ID|除 UserPrincipalName 以外的本地属性，例如 mail 属性，用于登录。|

## <a name="what-is-userprincipalname"></a>什么是 UserPrincipalName？
UserPrincipalName 是基于 Internet 标准 [RFC 822](http://www.ietf.org/rfc/rfc0822.txt) 的属性，表示某个用户的 Internet 样式登录名。 

### <a name="upn-format"></a>UPN 格式
UPN 由 UPN 前缀（用户帐户名）和 UPN 后缀（DNS 域名）组成。 前缀与后缀以“\@”符号相联接。 例如，“someone\@example.com”。 UPN 必须在目录林中的所有安全主体对象之间保持唯一。 

## <a name="upn-in-azure-ad"></a>Azure AD 中的 UPN 
Azure AD 使用 UPN 让用户登录。  用户可以使用的 UPN 取决于域是否经过验证。  如果域已验证，则允许具有该后缀的用户名登录到 Azure AD。  

该属性由 Azure AD Connect 同步。  在安装期间，可以查看已验证和未验证的域。

   ![未验证的域](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>备用登录 ID
在一些环境中，最终用户可能仅知道自己的电子邮件地址，但不知道自己的 UPN。  使用电子邮件地址的原因可能是公司策略或本地业务线应用程序依赖项。

备用登录 ID 允许配置登录体验，用户可以使用其 UPN 以外的属性（如邮件）登录。

若要在 Azure AD 中启用备用登录 ID，可以使用 Azure AD Connect，而无需执行任何附加的配置步骤。 可以直接通过向导配置备用 ID。 请参阅“同步”部分中适用于用户的 Azure AD 登录配置。在“用户主体名称”下拉列表中，选择“备用登录 ID”对应的属性。

![未验证的域](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

有关详细信息，请参阅[配置备用登录 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) 和 [Azure AD 登录配置](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>未验证的 UPN 后缀
如果本地 UserPrincipalName 属性/备用登录 ID 后缀未在 Azure AD 租户中验证，则 Azure AD UserPrincipalName 属性值将设置为 MOERA。 Azure AD 基于 Azure AD MailNickName 属性和 &lt;MailNickName&gt;&#64;&lt;初始域&gt; 形式的 Azure AD 初始域计算 MOERA。

## <a name="verified-upn-suffix"></a>已验证的 UPN 后缀
如果本地 UserPrincipalName 属性/备用登录 ID 后缀已在 Azure AD 租户中验证，则 Azure AD UserPrincipalName 属性值与本地 UserPrincipalName 属性/备用登录 ID 值相同。

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD MailNickName 属性值计算
由于 Azure AD UserPrincipalName 属性值可能设置为 MOERA，因此，必须了解 Azure AD MailNickName 属性值（MOERA 前缀）的计算方式。

将用户对象首次同步到 Azure AD 租户后，Azure AD 会按顺序检查以下各项，并将 MailNickName 属性值设置为现有的第一个值：

- 本地 mailNickName 属性
- 主要 SMTP 地址的前缀
- 本地 mail 属性的前缀
- 本地 userPrincipalName 属性/备用登录 ID 的前缀
- 次要 smtp 地址的前缀

将用户对象更新同步到 Azure AD 租户后，仅当本地 mailNickName 属性值发生更新时，Azure AD 才会更新 MailNickName 属性值。

>[!IMPORTANT]
>仅当已将本地 UserPrincipalName 属性/备用登录 ID 值更新同步到 Azure AD 租户时，Azure AD 才会重新计算 UserPrincipalName 属性值。 
>
>每当 Azure AD 重新计算 UserPrincipalName 属性时，也会重新计算 MOERA。 

## <a name="upn-scenarios"></a>UPN 方案
下面是根据给定的方案计算 UPN 的示例方案。

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>方案 1：未验证的 UPN 后缀 – 初始同步

![方案 1](media/active-directory-aadconnect-userprincipalname/example1.png)

本地用户对象：
- mailNickName：&lt;未设置&gt;
- proxyAddresses：{SMTP:us1@contoso.com}
- mail：us2@contoso.com
- userPrincipalName：us3@contoso.com`

首次将用户对象同步到 Azure AD 租户
- 将 Azure AD MailNickName 属性设置为主要 SMTP 地址前缀。
- 将 MOERA 设置为 &lt;MailNickName&gt;&#64;&lt;初始域&gt;。
- 将 Azure AD UserPrincipalName 属性设置为 MOERA。

Azure AD 租户用户对象：
- MailNickName：us1           
- UserPrincipalName：us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>方案 2：未验证的 UPN 后缀 – 设置本地 mailNickName 属性

![方案 2](media/active-directory-aadconnect-userprincipalname/example2.png)

本地用户对象：
- mailNickName：us4
- proxyAddresses：{SMTP:us1@contoso.com}
- mail：us2@contoso.com
- userPrincipalName：us3@contoso.com

将本地 mailNickName 属性更新同步到 Azure AD 租户
- 使用本地 mailNickName 属性更新 Azure AD MailNickName 属性。
- 由于本地 userPrincipalName 属性未更新，因此 Azure AD UserPrincipalName 属性未更改。

Azure AD 租户用户对象：
- MailNickName：us4
- UserPrincipalName：us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>方案 3：未验证的 UPN 后缀 – 更新本地 userPrincipalName 属性

![方案 3](media/active-directory-aadconnect-userprincipalname/example3.png)

本地用户对象：
- mailNickName：us4
- proxyAddresses：{SMTP:us1@contoso.com}
- mail：us2@contoso.com
- userPrincipalName：us5@contoso.com

将本地 userPrincipalName 属性更新同步到 Azure AD 租户
- 本地 userPrincipalName 属性更新触发 MOERA 和 Azure AD UserPrincipalName 属性的重新计算。
- 将 MOERA 设置为 &lt;MailNickName&gt;&#64;&lt;初始域&gt;。
- 将 Azure AD UserPrincipalName 属性设置为 MOERA。

Azure AD 租户用户对象：
- MailNickName：us4
- UserPrincipalName：us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>方案 4：未验证的 UPN 后缀 – 更新主要 SMTP 地址和本地 mail 属性

![方案 4](media/active-directory-aadconnect-userprincipalname/example4.png)

本地用户对象：
- mailNickName：us4
- proxyAddresses：{SMTP:us6@contoso.com}
- mail：us7@contoso.com
- userPrincipalName：us5@contoso.com

将本地 mail 属性和主要 SMTP 地址的更新同步到 Azure AD 租户
- 完成用户对象的初始同步后，本地 mail 属性和主要 SMTP 地址的更新不会影响 Azure AD MailNickName 或 UserPrincipalName 属性。

Azure AD 租户用户对象：
- MailNickName：us4
- UserPrincipalName：us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>方案 5：已验证的 UPN 后缀 – 更新本地 userPrincipalName 属性后缀

![方案 5](media/active-directory-aadconnect-userprincipalname/example5.png)

本地用户对象：
- mailNickName：us4
- proxyAddresses：{SMTP:us6@contoso.com}
- mail：us7@contoso.com
- serPrincipalName：us5@verified.contoso.com

将本地 userPrincipalName 属性更新同步到 Azure AD 租户
- 本地 userPrincipalName 属性更新触发 Azure AD UserPrincipalName 属性的重新计算。
- 由于 UPN 后缀已在 Azure AD 租户中验证，因此会将 Azure AD UserPrincipalName 属性设置为本地 userPrincipalName 属性。

Azure AD 租户用户对象：
- MailNickName：us4     
- UserPrincipalName：us5@verified.contoso.com

## <a name="next-steps"></a>后续步骤
- [将本地目录与 Azure Active Directory 进行集成](active-directory-aadconnect.md)
- [Azure AD Connect 的自定义安装](active-directory-aadconnect-get-started-custom.md)
