---
title: 自定义 Azure AD 应用 SAML 令牌声明
titleSuffix: Microsoft identity platform
description: 了解如何为 Azure AD 中的企业应用程序自定义 SAML 令牌中颁发的声明。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2019
ms.author: kenwith
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 5cce985e3f63ade94fb626d18bded440caeff1fa
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87274462"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>如何：为企业应用程序自定义 SAML 令牌中颁发的声明

目前，Azure Active Directory (Azure AD) 支持使用大多数企业应用程序进行单一登录 (SSO)，包括 Azure AD 应用库中预集成的两个应用程序以及自定义应用程序。 当用户通过 Azure AD 使用 SAML 2.0 协议向应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌将用户登录，而不是提示输入用户名和密码。 这些 SAML 令牌包含有关用户已知的“声明”的信息片段。

“声明”是标识提供者在为某个用户颁发的令牌中陈述的有关该用户的信息。 在 [SAML 令牌](https://en.wikipedia.org/wiki/SAML_2.0)中，此数据通常包含在 SAML 属性语句中。 用户的唯一 ID（也称为名称标识符）通常显示在 SAML 主题中。

默认情况下，Azure AD 向包含 `NameIdentifier` 声明的应用程序颁发 SAML 令牌，该声明具有在 Azure AD 中可唯一标识用户的用户名值（称为用户主体名称）。 SAML 令牌还含有其他声明，其中包含用户的电子邮件地址、名字和姓氏。

若要查看或编辑 SAML 令牌中颁发给应用程序的声明，请在 Azure 门户中打开应用程序。 然后打开“用户属性和声明”部分。

![打开 Azure 门户中的“用户属性和声明”部分](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

有两个可能的原因使你可能需要编辑 SAML 令牌中颁发的声明：

* 应用程序要求 `NameIdentifier` 声明或 NameID 声明是非存储在 Azure AD 中的用户名（或用户主体名称）以外的其他内容。
* 应用程序已编写为需要一组不同的声明 URI 或声明值。

## <a name="editing-nameid"></a>编辑 NameID

要编辑 NameID（名称标识符值）。

1. 打开“名称标识符值”页面。
1. 选择属性或要应用于属性的转换。 也可以指定希望 NameID 声明具有的格式。

   ![编辑 NameID（名称标识符）值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 格式

如果 SAML 请求包含具有特定格式的元素 NameIDPolicy，则 Azure AD 将遵循请求中的格式。

如果 SAML 请求不包含用于 NameIDPolicy 的元素，则 Azure AD 将使用指定的格式颁发 NameID。 如果未指定格式，Azure AD 将使用与所选声明源关联的默认源格式。

从**选择名称标识符格式**下拉列表中，可以选择以下一个选项。

| NameID 格式 | 说明 |
|---------------|-------------|
| **Default** | Azure AD 将使用默认的源格式。 |
| **Persistent** | Azure AD 将使用永久格式作为 NameID 格式。 |
| **EmailAddress** | Azure AD 将使用 EmailAddress 作为 NameID 格式。 |
| **Unspecified** | Azure AD 将使用未指定作为 NameID 格式。 |
| **Windows 域限定名** | Azure AD 将使用 WindowsDomainQualifiedName 作为 NameID 格式。 |

还支持临时 NameID，但在下拉列表中不可用，并且不能在 Azure 端进行配置。 若要了解有关 NameIDPolicy 属性的详细信息，请参阅 [单一登录 SAML 协议](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>属性

为 `NameIdentifier`（或 NameID）声明选择所需的源。 可以从以下选项中选择。

| 名称 | 说明 |
|------|-------------|
| 电子邮件 | 用户的电子邮件地址 |
| userprincipalName | 用户的用户主体名称 (UPN) |
| onpremisessamaccount | 已从本地 Azure AD 同步的 SAM 帐户名 |
| objectid | Azure AD 中用户的 objectID |
| employeeid | 用户的员工 ID |
| 目录扩展 | 目录扩展[使用 Azure AD Connect 同步从本地 Active Directory 同步](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 扩展属性 1-15 | 用于扩展 Azure AD 架构的本地扩展属性 |

有关详细信息，请参阅[表 3：每个源的有效 ID 值](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)。

还可以将任何常量（静态）值分配给在 Azure AD 中定义的任何声明。 请按照以下步骤分配常量值：

1. 在 [Azure 门户](https://portal.azure.com/)中，在“用户属性和声明”部分，单击“编辑”图标以编辑声明。 

1. 单击要修改的必选声明。

1. 在“源属性”中按组织输入不带引号的常量值，并单击“保存”。 

    ![打开 Azure 门户中的“用户属性和声明”部分](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 常量值显示如下。

    ![打开 Azure 门户中的“用户属性和声明”部分](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>特定声明 - 转换

还可以使用声明转换函数。

| 函数 | 说明 |
|----------|-------------|
| **ExtractMailPrefix()** | 删除电子邮件地址或用户主体名称中的域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。 |
| **Join()** | 将属性与已验证的域联接。 如果所选用户标识符值具有域，则将提取用户名以追加所选的已验证域。 例如，如果选择电子邮件 (joe_smith@contoso.com) 作为用户标识符值，并选择 contoso.onmicrosoft.com 作为已验证的域，则将生成 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 将所选属性的字符转换为小写字符。 |
| **ToUpper()** | 将所选属性的字符转换为大写字符。 |

## <a name="adding-application-specific-claims"></a>添加特定于应用程序的声明

要添加特定于应用程序的声明：

1. 在“用户属性和声明”中，选择“添加新声明”打开“管理用户声明”网页。  
1. 输入声明的“名称”。 根据 SAML 规范，此值无需严格遵循 URI 模式。如果需要 URI 模式，可以将其放入**命名空间**字段。
1. 选择声明可检索其值的**源**。 可以从“源属性”下拉列表中选择一个用户属性，或者在用户属性作为声明发出之前对其应用转换。

### <a name="claim-transformations"></a>声明转换

对用户属性应用转换：

1. 在**管理声明**中，选择*转换*作为声明源，以打开**管理转换**页。
2. 从“转换”下拉列表中选择函数。 根据所选的函数，必须提供要在转换中评估的参数和常量值。 有关可用函数的详细信息，请参阅下表。
3. 若要应用多个转换，请单击**添加转换**。最多可对声明应用两个转换。 例如，可以先提取 `user.mail` 的电子邮件前缀。 然后，将字符串设为大写。

   ![编辑 NameID（名称标识符）值](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

可以使用以下功能转换声明。

| 函数 | 说明 |
|----------|-------------|
| **ExtractMailPrefix()** | 删除电子邮件地址或用户主体名称中的域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。 |
| **Join()** | 通过联接两个属性来创建新的值。 或者，可以在两个属性之间使用分隔符。 对于 NameID 声明转换，联接仅限已验证的域。 如果所选用户标识符值具有域，则将提取用户名以追加所选的已验证域。 例如，如果选择电子邮件 (joe_smith@contoso.com) 作为用户标识符值，并选择 contoso.onmicrosoft.com 作为已验证的域，则将生成 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 将所选属性的字符转换为小写字符。 |
| **ToUpper()** | 将所选属性的字符转换为大写字符。 |
| **Contains()** | 如果输入与指定的值匹配，则输出一个属性或常量。 否则，如果没有匹配项，则可以指定其他输出。<br/>例如，如果想要发出一个声明，其中的值为用户的电子邮件地址（如果包含域 @contoso.com），否则就需要输出用户主体名称。 为此，需要配置以下值：<br/>*参数 1(输入)* ：user.email<br/>*值*: "@contoso.com"<br/>参数 2(输出)：user.email<br/>参数 3（如果没有匹配项，则为输出）：user.userprincipalname |
| **EndWith()** | 如果输入以指定值结束，则输出一个属性或常量。 否则，如果没有匹配项，则可以指定其他输出。<br/>例如，如果想要发出一个声明，其中的值为用户的员工 ID （如果员工 ID 以 000 结束），否则就需要输出一个扩展属性。 为此，需要配置以下值：<br/>*参数 1(输入)* ：user.employeeid<br/>*值*：000<br/>参数 2(输出)：user.employeeid<br/>参数 3（如果没有匹配项，则为输出）：user.extensionattribute1 |
| **StartWith()** | 如果输入以指定值开始，则输出一个属性或常量。 否则，如果没有匹配项，则可以指定其他输出。<br/>例如，如果想要发出一个声明，其中的值为用户的员工 ID （如果国家/地区以“美国”开始），否则就需要输出一个扩展属性。 为此，需要配置以下值：<br/>*参数 1(输入)* ：user.country<br/>*值*：美国<br/>参数 2(输出)：user.employeeid<br/>参数 3（如果没有匹配项，则为输出）：user.extensionattribute1 |
| **Extract() - 匹配后** | 匹配指定值后返回的子字符串。<br/>例如，如果输入的值为“Finance_BSimon”，匹配值为“Finance_”，则声明的输出为“BSimon”。 |
| **Extract() - 匹配前** | 在匹配指定值前返回的子字符串。<br/>例如，如果输入的值为“BSimon_US”，匹配值为“_US”，则声明的输出为“BSimon”。 |
| **Extract() - 匹配之间** | 在匹配指定值前返回的子字符串。<br/>例如，如果输入的值为“Finance_BSimon_US”，第一个匹配值为“Finance_”，第二个匹配值为“_US”，则声明的输出为“BSimon”。 |
| **ExtractAlpha() - 前缀** | 返回字符串的前缀字母部分。<br/>例如，如果输入的值为“BSimon_123”，则它将返回“BSimon”。 |
| **ExtractAlpha() - 后缀** | 返回字符串的后缀字母部分。<br/>例如，如果输入的值为“123_Simon”，则它将返回“Simon”。 |
| **ExtractNumeric() - 前缀** | 返回字符串的前缀数字部分。<br/>例如，如果输入的值为“123_BSimon”，则它将返回“123”。 |
| **ExtractNumeric() - 后缀** | 返回字符串的后缀数字部分。<br/>例如，如果输入的值为“BSimon_123”，则它将返回“123”。 |
| **IfEmpty()** | 如果输入为 null 或为空，则输出一个属性或常量。<br/>例如，如果希望在给定用户的员工 ID 为空时输出存储在 extensionattribute 中的属性。 为此，需要配置以下值：<br/>参数 1(输入)：user.employeeid<br/>参数 2(输出)：user.extensionattribute1<br/>参数 3（如果没有匹配项，则为输出）：user.employeeid |
| **IfNotEmpty()** | 如果输入为 null 或为空，则输出一个属性或常量。<br/>例如，如果希望在给定用户的员工 ID 为空时输出存储在 extensionattribute 中的属性。 为此，需要配置以下值：<br/>参数 1(输入)：user.employeeid<br/>参数 2(输出)：user.extensionattribute1 |

如果需要其他转换，请在“SaaS 应用程序”类别下的 [Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)中发布你的想法。

## <a name="emitting-claims-based-on-conditions"></a>发出基于条件的声明

能够根据用户类型和用户所属的组来指定声明的源。 

用户类型可以是：
- **任何**：允许所有用户访问应用程序。
- **成员**：本机租户的成员
- **所有来宾**：从具有或没有 Azure AD 的外部组织中转来的用户。
- **AAD 来宾**：来宾用户属于使用 Azure AD的其他组织。
- **外部来宾**：来宾用户属于没有 Azure AD 的外部组织。


此场景在来宾和员工访问应用程序使用的声明源不同时非常有用。 可以指定如果用户是员工，则 NameID 来源于 user.email，但如果用户是来宾，则 NameID 来源于 user.extensionattribute1。

要添加声明条件：

1. 在**管理声明**中，展开”声明条件”。
2. 选择用户类型。
3. 选择用户所属的组。 在给定应用程序的所有声明中，最多可以选择50个唯一组。 
4. 选择声明可检索其值的**源**。 可以从“源属性”下拉列表中选择一个用户属性，或者在用户属性作为声明发出之前对其应用转换。

添加条件的顺序很重要。 Azure AD 将按从上到下的顺序评估条件，以确定要在声明中发出的值。 

例如，Britta Simon 是 Contoso 租户中的来宾用户。 她属于另一个也使用 Azure AD 的组织。 基于以下对 Fabrikam 应用程序的配置，如果 Britta 尝试登录到 Fabrikam，Azure AD 将按如下方式评估条件。

首先，Azure AD 会验证 Britta 的用户类型是否为 `All guests`。 因为它为 true，则 Azure AD 将声明的源分配给 `user.extensionattribute1`。 其次，Azure AD 会验证 Britta 的用户类型是否为 `AAD guests`，因为它也是 true，然后 Azure AD 将声明的源分配给 `user.mail`。 最后，使用值 `user.mail` 为 Britta 发出声明。

![声明条件配置](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>后续步骤

* [Azure AD 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [针对不在 Azure AD 应用程序库中的应用程序配置单一登录](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [排查基于 SAML 的单一登录的问题](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
