---
title: 在 Azure AD 中自定义企业应用的 SAML 令牌声明Microsoft Docs
description: 了解如何为 Azure AD 中的企业应用程序自定义 SAML 令牌中颁发的声明。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c0625a233b3b4a949feff2e289361a26fc8dc5a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835357"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>如何：为企业应用程序自定义 SAML 令牌中颁发的声明

目前, Azure Active Directory (Azure AD) 支持大多数企业应用程序的单一登录 (SSO), 包括在 Azure AD 应用程序库和自定义应用程序中预先集成的应用程序。 当用户通过 Azure AD 使用 SAML 2.0 协议向应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌将用户登录，而不是提示输入用户名和密码。 这些 SAML 令牌包含有关用户的信息片段 (称为 "*声明*")。

“声明”是标识提供者在为某个用户颁发的令牌中陈述的有关该用户的信息。 在 [SAML 令牌](https://en.wikipedia.org/wiki/SAML_2.0)中，此数据通常包含在 SAML 属性语句中。 用户的唯一 ID（也称为名称标识符）通常显示在 SAML 主题中。

默认情况下, Azure AD 向应用程序颁发 SAML 令牌, 其中包含`NameIdentifier`具有用户用户名 (也称为用户主体名称 Azure AD) 值的声明, 该声明可以唯一地标识用户。 SAML 令牌还含有其他声明，其中包含用户的电子邮件地址、名字和姓氏。

若要查看或编辑 SAML 令牌中颁发给应用程序的声明，请在 Azure 门户中打开应用程序。 然后打开 & 声明 "部分的"**用户属性**"。

![打开 Azure 门户中的 "用户属性" & 声明 "部分](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

有两个可能的原因使你可能需要编辑 SAML 令牌中颁发的声明：

* 应用程序要求`NameIdentifier`或 NameID 声明不是存储在 Azure AD 中的用户名 (或用户主体名称)。
* 应用程序已编写为需要一组不同的声明 URI 或声明值。

## <a name="editing-nameid"></a>编辑 NameID

编辑 NameID (名称标识符值):

1. 打开 "**名称标识符值**" 页。
1. 选择要应用于属性的属性或转换。 (可选) 可以指定希望 NameID 声明具有的格式。

   ![编辑 NameID (name 标识符) 值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 格式

如果 SAML 请求包含具有特定格式的元素 NameIDPolicy, 则 Azure AD 将遵循请求中的格式。

如果 SAML 请求不包含用于 NameIDPolicy 的元素, 则 Azure AD 将使用您指定的格式发出 NameID。 如果未指定格式 Azure AD 将使用与所选声明源关联的默认源格式。

从 "**选择名称标识符格式**" 下拉列表中, 可以选择以下选项之一。

| NameID 格式 | 描述 |
|---------------|-------------|
| **默认** | Azure AD 将使用默认的源格式。 |
| **式** | Azure AD 将使用永久性格式作为 NameID 格式。 |
| **电子邮件地址** | Azure AD 将使用 EmailAddress 作为 NameID 格式。 |
| **未指定** | Azure AD 将使用未指定的作为 NameID 格式。 |
| **瞬态** | Azure AD 将使用瞬态作为 NameID 格式。 |

若要了解有关 NameIDPolicy 属性的详细信息, 请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>特性

为 `NameIdentifier`（或 NameID）声明选择所需的源。 可以从以下选项中选择。

| 姓名 | 描述 |
|------|-------------|
| Email | 用户的电子邮件地址 |
| userprincipalName | 用户的用户主体名称 (UPN) |
| onpremisessamaccount | 已从本地 Azure AD 同步的 SAM 帐户名 |
| objectid | Azure AD 中的用户的 objectid |
| employeeid | 用户的雇员 id |
| 目录扩展 | 目录扩展[使用 Azure AD Connect 同步从本地 Active Directory 同步](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 扩展属性 1-15 | 用于扩展 Azure AD 架构的本地扩展属性 |

有关详细信息, 请[参阅表 3:每个源](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)的有效 ID 值。

### <a name="special-claims---transformations"></a>特殊声明-转换

你还可以使用声明转换函数。

| 函数 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 删除电子邮件地址或用户主体名称中的域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。 |
| **Join()** | 将属性与已验证的域联接。 如果所选用户标识符值具有域，则将提取用户名以追加所选的已验证域。 例如，如果选择电子邮件 (joe_smith@contoso.com) 作为用户标识符值，并选择 contoso.onmicrosoft.com 作为已验证的域，则将生成 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 将所选属性的字符转换为小写字符。 |
| **ToUpper()** | 将所选属性的字符转换为大写字符。 |

## <a name="adding-application-specific-claims"></a>添加特定于应用程序的声明

添加特定于应用程序的声明:

1. 在 "**用户属性" & 声明**中, 选择 "**添加新声明**" 以打开 "**管理用户声明**" 页。
1. 输入声明的**名称**。 根据 SAML 规范, 此值并不严格需要遵循 URI 模式。如果需要 URI 模式, 可以将其放入**命名空间**字段中。
1. 选择声明要检索其值的**源**。 你可以从 "源属性" 下拉列表中选择一个用户属性, 或在将其作为声明发出之前向用户属性应用转换。

### <a name="application-specific-claims---transformations"></a>应用程序特定的声明-转换

你还可以使用声明转换函数。

| 函数 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 删除电子邮件地址或用户主体名称中的域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。 |
| **Join()** | 通过联接两个属性来创建新的值。 或者, 您可以在两个属性之间使用分隔符。 |
| **ToLower()** | 将所选属性的字符转换为小写字符。 |
| **ToUpper()** | 将所选属性的字符转换为大写字符。 |
| **Contains()** | 如果输入与指定的值匹配, 则输出一个属性或常量。 否则, 如果没有匹配项, 则可以指定其他输出。<br/>例如, 如果你想要发出一个声明, 其中值为用户的电子邮件地址 (如果它包含域 "@contoso.com"), 否则你需要输出用户主体名称。 为此, 需要配置以下值:<br/>*参数 1 (输入)* : user. email<br/>*值*: "@contoso.com"<br/>参数 2 (输出): user. email<br/>参数 3 (如果没有匹配项, 则为输出): 用户 userprincipalname |
| **EndWith()** | 如果输入以指定的值结束, 则输出特性或常数。 否则, 如果没有匹配项, 则可以指定其他输出。<br/>例如, 如果您想要发出一个声明, 其中值为用户的雇员 id (如果雇员 id 以 "000" 结尾), 否则您需要输出一个扩展属性。 为此, 需要配置以下值:<br/>*参数 1 (输入)* : 用户 id<br/>*值*：000<br/>参数 2 (输出): 用户 id<br/>参数 3 (如果没有匹配项, 则为输出): extensionattribute1 |
| **StartWith()** | 如果输入以指定值开头, 则输出特性或常数。 否则, 如果没有匹配项, 则可以指定其他输出。<br/>例如, 如果你想要发出一个声明, 其中值为用户的雇员 id (如果国家/地区以 "US" 开头), 否则你需要输出一个扩展属性。 为此, 需要配置以下值:<br/>*参数 1 (输入)* : 用户所在的国家/地区<br/>*值*：反馈<br/>参数 2 (输出): 用户 id<br/>参数 3 (如果没有匹配项, 则为输出): extensionattribute1 |
| **提取 ()-匹配后** | 返回与指定的值匹配的子字符串。<br/>例如, 如果输入的值为 "Finance_BSimon", 则匹配值为 "Finance_", 则声明的输出为 "BSimon"。 |
| **提取 ()-匹配之前** | 返回子字符串, 直到它与指定的值匹配。<br/>例如, 如果输入的值为 "BSimon_US", 则匹配值为 "_US", 则声明的输出为 "BSimon"。 |
| **提取 ()-匹配时** | 返回子字符串, 直到它与指定的值匹配。<br/>例如, 如果输入的值为 "Finance_BSimon_US", 则第一个匹配值为 "Finance_", 第二个匹配值为 "_US", 则声明的输出为 "BSimon"。 |
| **ExtractAlpha ()-前缀** | 返回字符串的前缀字母部分。<br/>例如, 如果输入的值为 "BSimon_123", 则它将返回 "BSimon"。 |
| **ExtractAlpha ()-后缀** | 返回字符串的后缀字母部分。<br/>例如, 如果输入的值为 "123_Simon", 则它将返回 "Simon"。 |
| **ExtractNumeric ()-前缀** | 返回字符串的前缀数字部分。<br/>例如, 如果输入的值为 "123_BSimon", 则它将返回 "123"。 |
| **ExtractNumeric ()-后缀** | 返回字符串的后缀数值部分。<br/>例如, 如果输入的值为 "BSimon_123", 则它将返回 "123"。 |
| **IfEmpty()** | 如果输入为 null 或为空, 则输出属性或常量。<br/>例如, 如果要在给定用户的雇员 id 为空时输出存储在 system.runtime.compilerservices.extensionattribute 中的属性, 则为。 为此, 需要配置以下值:<br/>参数 1 (输入): 用户 id<br/>参数 2 (输出): extensionattribute1<br/>参数 3 (如果没有匹配项, 则为输出): 用户 id |
| **IfNotEmpty()** | 如果输入的值不为 null 或为空, 则输出属性或常量。<br/>例如, 如果要输出 system.runtime.compilerservices.extensionattribute 中存储的属性 (如果给定用户的雇员 id 不为空)。 为此, 需要配置以下值:<br/>参数 1 (输入): 用户 id<br/>参数 2 (输出): extensionattribute1 |

如果需要更多的转换, 请在 " *SaaS 应用程序*" 类别下[Azure AD 的反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)中提交你的想法。

## <a name="next-steps"></a>后续步骤

* [Azure AD 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [针对不在 Azure AD 应用程序库中的应用程序配置单一登录](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [排查基于 SAML 的单一登录的问题](howto-v1-debug-saml-sso-issues.md)
