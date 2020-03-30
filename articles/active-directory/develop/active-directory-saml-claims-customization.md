---
title: 自定义 Azure AD 应用 SAML 令牌声明
titleSuffix: Microsoft identity platform
description: 了解如何为 Azure AD 中的企业应用程序自定义 SAML 令牌中颁发的声明。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 52779b7ffea0f33676426f145a700c7181cf0bf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263083"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>如何：为企业应用程序自定义 SAML 令牌中发出的声明

如今，Azure 活动目录 （Azure AD） 支持大多数企业应用程序的单一登录 （SSO），包括预先集成在 Azure AD 应用库中的应用程序以及自定义应用程序。 当用户通过 Azure AD 使用 SAML 2.0 协议向应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌将用户登录，而不是提示输入用户名和密码。 这些 SAML 令牌包含有关称为*声明*的用户的信息片段。

“声明”** 是标识提供者在为某个用户颁发的令牌中陈述的有关该用户的信息。 在 [SAML 令牌](https://en.wikipedia.org/wiki/SAML_2.0)中，此数据通常包含在 SAML 属性语句中。 用户的唯一 ID（也称为名称标识符）通常显示在 SAML 主题中。

默认情况下，Azure AD 向应用程序发出 SAML 令牌，其中包含具有`NameIdentifier`Azure AD 中用户名（也称为用户主体名称）的值的声明，该声明可以唯一标识用户。 SAML 令牌还含有其他声明，其中包含用户的电子邮件地址、名字和姓氏。

若要查看或编辑 SAML 令牌中颁发给应用程序的声明，请在 Azure 门户中打开应用程序。 然后打开 **"用户属性&声明**"部分。

![在 Azure 门户中打开用户属性&声明部分](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

有两个可能的原因使你可能需要编辑 SAML 令牌中颁发的声明：

* 应用程序要求`NameIdentifier`或 NameID 声明是存储在 Azure AD 中的用户名（或用户主体名称）以外的内容。
* 应用程序已编写为需要一组不同的声明 URI 或声明值。

## <a name="editing-nameid"></a>编辑名称 ID

要编辑 NameID（名称标识符值）：

1. 打开**名称标识符值**页。
1. 选择要应用于该属性的属性或转换。 或者，您可以指定希望 NameID 声明具有的格式。

   ![编辑 NameID（名称标识符）值](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>名称 ID 格式

如果 SAML 请求包含具有特定格式的元素 NameID 策略，则 Azure AD 将遵守请求中的格式。

如果 SAML 请求不包含 NameID 策略的元素，则 Azure AD 将发出名称 ID 与您指定的格式。 如果未指定格式，则 Azure AD 将使用与所选声明源关联的默认源格式。

从 **"选择名称标识符"格式**下拉下拉列表，您可以选择以下选项之一。

| 名称 ID 格式 | 描述 |
|---------------|-------------|
| **默认** | Azure AD 将使用默认源格式。 |
| **持续** | Azure AD 将使用"持久"作为 NameID 格式。 |
| **电子邮件地址** | Azure AD 将使用电子邮件地址作为 NameID 格式。 |
| **未指定** | Azure AD 将使用"未指定"格式作为 NameID 格式。 |
| **视窗域名限定名称** | Azure AD 将使用 WindowsDomain 资格名称作为名称 ID 格式。 |

临时 NameID 也受支持，但在下拉列表中不可用，无法在 Azure 端配置。 要了解有关 NameID 策略属性的更多详细信息，请参阅[单一登录 SAML 协议](single-sign-on-saml-protocol.md)。

### <a name="attributes"></a>特性

为 `NameIdentifier`（或 NameID）声明选择所需的源。 可以从以下选项中选择。

| “属性” | 描述 |
|------|-------------|
| 电子邮件 | 用户的电子邮件地址 |
| userprincipalName | 用户的主名 （UPN） |
| onpremisessamaccount | 已从本地 Azure AD 同步的 SAM 帐户名 |
| objectid | Azure AD 中用户的对象 ID |
| employeeid | 用户的员工 ID |
| 目录扩展 | 目录扩展[使用 Azure AD Connect 同步从本地 Active Directory 同步](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 扩展属性 1-15 | 用于扩展 Azure AD 架构的本地扩展属性 |

有关详细信息，请参阅表[3：每个源的有效 ID 值](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)。

您还可以为在 Azure AD 中定义的任何声明分配任何常量（静态）值。 请按照以下步骤分配常量值：

1. 在[Azure 门户](https://portal.azure.com/)中，在 **"用户属性&声明"** 部分，单击 **"编辑"** 图标编辑声明。

1. 单击要修改所需的声明。

1. 根据您的组织在 **"源"属性**中输入无引号的常量值，然后单击"**保存**"。

    ![在 Azure 门户中打开用户属性&声明部分](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 常量值将显示如下。

    ![在 Azure 门户中打开用户属性&声明部分](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>特殊索赔 - 转换

您还可以使用声明转换函数。

| 函数 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 从电子邮件地址或用户主体名称中删除域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。 |
| **加入（）** | 将属性与已验证的域联接。 如果所选用户标识符值具有域，则将提取用户名以追加所选的已验证域。 例如，如果选择电子邮件 (joe_smith@contoso.com) 作为用户标识符值，并选择 contoso.onmicrosoft.com 作为已验证的域，则将生成 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 将所选属性的字符转换为小写字符。 |
| **ToUpper()** | 将所选属性的字符转换为大写字符。 |

## <a name="adding-application-specific-claims"></a>添加特定于应用程序的声明

要添加特定于应用程序的声明：

1. 在 **"用户属性&声明**"中，选择 **"添加新声明**"以打开 **"管理用户声明**"页。
1. 输入声明**的名称**。 根据 SAML 规范，该值不需要严格遵循 URI 模式。如果需要 URI 模式，可以把它放在**命名空间**字段中。
1. 选择声明要检索其值的**源**。 您可以从源属性下拉列表中选择用户属性，或在将转换作为声明发出之前对用户属性应用转换。

### <a name="claim-transformations"></a>声明转换

要将转换应用于用户属性，请执行：

1. 在 **"管理索赔"** 中，选择 *"转换*"作为声明源以打开 **"管理转换**"页。
2. 从转换下拉列表中选择函数。 根据所选的函数，您必须提供参数和常量值才能在转换中计算。 有关可用函数的详细信息，请参阅下表。
3. 要应用多个转换，请单击"**添加转换**"。最多可以对索赔应用两个转换。 例如，您可以首先提取 的电子邮件`user.mail`前缀。 然后，使字符串大写。

   ![编辑 NameID（名称标识符）值](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

您可以使用以下函数转换声明。

| 函数 | 描述 |
|----------|-------------|
| **ExtractMailPrefix()** | 从电子邮件地址或用户主体名称中删除域后缀。 这只会提取传递用户名的第一部分（例如，“joe_smith”而不是 joe_smith@contoso.com）。 |
| **加入（）** | 通过联接两个属性创建新值。 或者，您可以在两个属性之间使用分隔符。 对于 NameID 声明转换，联接仅限于已验证的域。 如果所选用户标识符值具有域，则将提取用户名以追加所选的已验证域。 例如，如果选择电子邮件 (joe_smith@contoso.com) 作为用户标识符值，并选择 contoso.onmicrosoft.com 作为已验证的域，则将生成 joe_smith@contoso.onmicrosoft.com。 |
| **ToLower()** | 将所选属性的字符转换为小写字符。 |
| **ToUpper()** | 将所选属性的字符转换为大写字符。 |
| **包含（）** | 如果输入与指定值匹配，则输出属性或常量。 否则，如果没有匹配，可以指定另一个输出。<br/>例如，如果要发出值是用户的电子邮件地址的声明（如果它包含域""），@contoso.com则要输出用户主体名称。 为此，您可以配置以下值：<br/>*参数 1（输入）*：用户.电子邮件<br/>*值*："@contoso.com"<br/>参数 2（输出）：用户.电子邮件<br/>参数 3（如果没有匹配项，则输出）：用户.user主名称 |
| **结束与（）** | 如果输入以指定值结束，则输出属性或常量。 否则，如果没有匹配，可以指定另一个输出。<br/>例如，如果要发出值是用户的员工 ID 的声明（如果员工 ID 以"000"结尾），则要输出扩展属性。 为此，您可以配置以下值：<br/>*参数 1（输入）*：用户.员工id<br/>*值*："000"<br/>参数 2（输出）：用户.员工 ID<br/>参数 3（如果没有匹配时输出）：用户.扩展属性1 |
| **从（） 开始** | 如果输入以指定值开头，则输出属性或常量。 否则，如果没有匹配，可以指定另一个输出。<br/>例如，如果要发出值是用户的员工 ID 的声明（如果国家/地区以"US"开头），则要输出扩展属性。 为此，您可以配置以下值：<br/>*参数 1（输入）*：用户.国家/地区<br/>*值*："美国"<br/>参数 2（输出）：用户.员工 ID<br/>参数 3（如果没有匹配时输出）：用户.扩展属性1 |
| **提取（） - 匹配后** | 在子字符串与指定值匹配后返回该子字符串。<br/>例如，如果输入的值为"Finance_BSimon"，则匹配值为"Finance_"，则声明的输出为"BSimon"。 |
| **提取（） - 匹配前** | 返回子字符串，直到它与指定值匹配。<br/>例如，如果输入的值为"BSimon_US"，则匹配值为"_US"，则声明的输出为"BSimon"。 |
| **提取（） - 匹配之间** | 返回子字符串，直到它与指定值匹配。<br/>例如，如果输入的值为"Finance_BSimon_US"，则第一个匹配值为"Finance_"，第二个匹配值为"_US"，则声明的输出为"BSimon"。 |
| **提取Alpha（） - 前缀** | 返回字符串的前缀字母部分。<br/>例如，如果输入的值为"BSimon_123"，则返回"BSimon"。 |
| **提取Alpha（） - 后缀** | 返回字符串的后缀字母部分。<br/>例如，如果输入的值为"123_Simon"，则返回"Simon"。 |
| **提取数字（） - 前缀** | 返回字符串的前缀数字部分。<br/>例如，如果输入的值为"123_BSimon"，则返回"123"。 |
| **提取数字（） - 后缀** | 返回字符串的后缀数值部分。<br/>例如，如果输入的值为"BSimon_123"，则返回"123"。 |
| **如果空（）** | 如果输入为空或为空，则输出属性或常量。<br/>例如，如果要输出存储在扩展属性中的属性，如果给定用户的员工 ID 为空。 为此，您可以配置以下值：<br/>参数 1（输入）：用户.员工 ID<br/>参数 2（输出）：用户.扩展属性1<br/>参数 3（如果没有匹配项，则输出）：用户.employeeid |
| **如果不为空（）** | 如果输入不是空或空，则输出属性或常量。<br/>例如，如果要输出存储在扩展属性中的属性，如果给定用户的员工 ID 不为空。 为此，您可以配置以下值：<br/>参数 1（输入）：用户.员工 ID<br/>参数 2（输出）：用户.扩展属性1 |

如果需要其他转换，请在*SaaS 应用程序*类别下的[Azure AD 反馈论坛中](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599)提交您的想法。

## <a name="emitting-claims-based-on-conditions"></a>根据条件发出声明

您可以根据用户类型和用户所属的组指定声明的来源。 

用户类型可以是：
- **任何**：允许所有用户访问该应用程序。
- **成员**： 租户的本机成员
- **所有来宾**：用户是从具有或没有 Azure AD 的外部组织带来的。
- **AAD 来宾**：来宾用户属于使用 Azure AD 的另一个组织。
- **外部来宾**：来宾用户属于没有 Azure AD 的外部组织。


其中一种情况是，对于访问应用程序的来宾和员工，索赔的来源是不同的。 您可能希望指定，如果用户是员工，NameID 来自 user.email，但如果用户是来宾，则 NameID 来自用户。

要添加声明条件：

1. 在**管理索赔**中，展开索赔条件。
2. 选择用户类型。
3. 选择用户应所属的组。 您最多可以在给定应用程序的所有声明中选择 10 个唯一组。 
4. 选择声明要检索其值的**源**。 您可以从源属性下拉列表中选择用户属性，或在将转换作为声明发出之前对用户属性应用转换。

添加条件的顺序很重要。 Azure AD 从上到下评估条件，以决定在索赔中释放哪个值。 

例如，Brita Simon 是 Contoso 租户中的来宾用户。 她属于另一个也使用 Azure AD 的组织。 给定 Fabrikam 应用程序的以下配置，当 Brita 尝试登录到 Fabrikam 时，Azure AD 将按如下条件评估条件。

首先，Azure AD 验证 Brita 的用户类型是否为`All guests`。 由于，这是事实，然后 Azure AD 将声明的源分配给`user.extensionattribute1`。 其次，Azure AD 验证 Brita 的用户类型是否为`AAD guests`，因为这也是 true，然后 Azure AD 将声明的源`user.mail`分配给 。 最后，声明以"Brita"`user.email`的值发出。

![声明条件配置](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>后续步骤

* [Azure AD 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [针对不在 Azure AD 应用程序库中的应用程序配置单一登录](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [排查基于 SAML 的单一登录的问题](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
