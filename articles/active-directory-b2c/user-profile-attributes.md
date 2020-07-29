---
title: Azure Active Directory B2C 中的用户配置文件属性
description: 了解 Azure AD B2C 目录用户配置文件支持的用户资源类型属性。 了解内置属性、扩展以及属性如何映射到 Microsoft Graph。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738551"
---
# <a name="user-profile-attributes"></a>用户配置文件属性

Azure Active Directory (Azure AD) B2C 目录用户配置文件附带了一组内置属性，例如名字、姓氏、城市、邮政编码和电话号码。 可以使用自己的应用程序数据来扩展用户配置文件，而无需外部数据存储。 可用于 Azure AD B2C 用户配置文件的大多数属性也受 Microsoft Graph 的支持。 本文介绍了支持的 Azure AD B2C 用户配置文件属性。 本文还说明了 Microsoft Graph 不支持的属性，以及不应该用于 Azure AD B2C 的 Microsoft Graph 属性。

> [!IMPORTANT]
> 不应使用内置或扩展属性来存储敏感的个人数据，例如帐户凭据、政府身份证号、持卡人数据、财务帐户数据、医疗保健信息或敏感背景信息。

还可以与外部系统相集成。 例如，可以使用 Azure AD B2C 进行身份验证，但将权限委托给用作客户数据的权威来源的外部客户关系管理 (CRM) 或客户忠诚度数据库。 有关详细信息，请参阅[远程配置文件](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解决方案。

下表列出了 Azure AD B2C 目录用户配置文件支持的[用户资源类型](https://docs.microsoft.com/graph/api/resources/user)属性。 其中提供了有关每个属性的以下信息：

- Azure AD B2C 使用的属性名称（如果不同，后面的括号中会包含 Microsoft Graph 名称）
- 属性数据类型
- 属性说明
- 该属性是否可在 Azure 门户中可用
- 该属性是否可在用户流中使用
- 该属性是否可在自定义策略 [Azure AD 技术配置文件](active-directory-technical-profile.md)中使用，以及可在哪个部分（&lt;InputClaims&gt;、&lt;OutputClaims&gt; 或 &lt;PersistedClaims&gt;）中使用

|名称     |类型     |说明|Azure 门户|用户流|自定义策略|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|用户帐户是已启用还是已禁用：如果已启用帐户，则为“true”，否则为“false” 。|是|否|持久化、输出|
|ageGroup        |字符串|用户的年龄组。 可能的值：null、Undefined、Minor、Adult、NotAdult。|是|否|持久化、输出|
|alternativeSecurityId（[标识](manage-user-accounts-graph-api.md#identities-property)）|字符串|来自外部标识提供者的单个用户标识。|否|否|输入、持久化、输出|
|alternativeSecurityIds（[标识](manage-user-accounts-graph-api.md#identities-property)）|备用 securityId 集合|来自外部标识提供者的用户标识集合。|否|否|持久化、输出|
|city            |字符串|用户所在的城市。 最大长度为 128。|是|是|持久化、输出|
|consentProvidedForMinor|字符串|是否为未成年人提供了许可。 允许的值：null、granted、denied 或 notRequired。|是|否|持久化、输出|
|country         |字符串|用户所在的国家/地区。 示例：“US”或“UK”。 最大长度为 128。|是|是|持久化、输出|
|createdDateTime|DateTime|创建用户对象的日期。 只读。|否|否|持久化、输出|
|creationType    |字符串|如果用户帐户是作为 Azure Active Directory B2C 租户的本地帐户创建的，则值为 LocalAccount 或 nameCoexistence。 只读。|否|否|持久化、输出|
|dateOfBirth     |Date|出生日期。|否|否|持久化、输出|
|department      |字符串|用户所在部门的名称。 最大长度为 64。|是|否|持久化、输出|
|displayName     |字符串|用户的显示名称。 最大长度为 256。|是|是|持久化、输出|
|facsimileTelephoneNumber<sup>1</sup>|字符串|用户的业务传真号码。|是|否|持久化、输出|
|givenName       |字符串|用户的名字（名）。 最大长度为 64。|是|是|持久化、输出|
|jobTitle        |字符串|用户的职务。 最大长度为 128。|是|是|持久化、输出|
|immutableId     |字符串|从本地 Active Directory 迁移的用户通常使用的标识符。|否|否|持久化、输出|
|legalAgeGroupClassification|字符串|法定年龄组分类。 基于 ageGroup 和 consentProvidedForMinor 属性计算的只读属性。 允许的值：null、minorWithOutParentalConsent、minorWithParentalConsent、minorNoParentalConsentRequired、notAdult 和 adult。|是|否|持久化、输出|
|legalCountry<sup>1</sup>  |字符串|用于法律目的的国家/地区。|否|否|持久化、输出|
|mail            |字符串|用户的 SMTP 地址，例如“bob@contoso.com”。 只读。|否|否|持久化、输出|
|mailNickName    |字符串|用户的邮件别名。 最大长度为 64。|否|否|持久化、输出|
|mobile (mobilePhone) |字符串|用户的主要手机号码。 最大长度为 64。|是|否|持久化、输出|
|netId           |字符串|网络 ID。|否|否|持久化、输出|
|objectId        |字符串|全局唯一标识符 (GUID)（用户的唯一标识符）。 示例：12345678-9abc-def0-1234-56789abcde。 只读，不可变。|只读|是|输入、持久化、输出|
|otherMails      |字符串集合|用户的其他电子邮件地址列表。 示例：[“bob@contoso.com”、“Robert@fabrikam.com”]。|是（备用电子邮件）|否|持久化、输出|
|password        |字符串|创建用户期间用于本地帐户的密码。|否|否|持久化|
|passwordPolicies     |字符串|密码策略。 它是由逗号分隔的不同策略名称构成的字符串。 例如“DisablePasswordExpiration, DisableStrongPassword”。|否|否|持久化、输出|
|physicalDeliveryOfficeName (officeLocation)|字符串|用户营业地点的办公室位置。 最大长度为 128。|是|否|持久化、输出|
|postalCode      |字符串|用户邮政地址的邮政编码。 该邮政编码特定于用户所在的国家/地区。 在美国，此属性包含邮政编码。 最大长度为 40。|是|否|持久化、输出|
|preferredLanguage    |字符串|用户的首选语言。 应遵循 ISO 639-1 代码。 示例：“en-US”。|否|否|持久化、输出|
|refreshTokensValidFromDateTime|DateTime|在此时间之前颁发的所有刷新令牌无效，使用无效刷新令牌获取新的访问令牌时，应用程序会收到错误。 如果发生这种情况，应用程序需要通过向授权终结点发出请求来获取新的刷新令牌。 只读。|否|否|输出|
|signInNames（[标识](manage-user-accounts-graph-api.md#identities-property)） |字符串|目录中任何类型的本地帐户用户的唯一登录名。 使用此属性可以获取具有登录值的用户，而无需指定本地帐户类型。|否|否|输入|
|signInNames.userName（[标识](manage-user-accounts-graph-api.md#identities-property)） |字符串|目录中本地帐户用户的唯一用户名。 使用此属性可以创建或获取具有特定登录用户名的用户。 在执行修补操作期间，在 PersistedClaims 中单独指定此属性会删除其他 signInNames 类型。 若要添加新的 signInNames 类型，还需要保存现有的 signInNames。|否|否|输入、持久化、输出|
|signInNames.phoneNumber（[标识](manage-user-accounts-graph-api.md#identities-property)） |字符串|目录中本地帐户用户的唯一电话号码。 使用此属性可以创建或获取具有特定登录电话号码的用户。 在执行修补操作期间，在 PersistedClaims 中单独指定此属性会删除其他 signInNames 类型。 若要添加新的 signInNames 类型，还需要保存现有的 signInNames。|否|否|输入、持久化、输出|
|signInNames.emailAddress（[标识](manage-user-accounts-graph-api.md#identities-property)）|字符串|目录中本地帐户用户的唯一电子邮件地址。 使用此属性可以创建或获取具有特定登录电子邮件地址的用户。 在执行修补操作期间，在 PersistedClaims 中单独指定此属性会删除其他 signInNames 类型。 若要添加新的 signInNames 类型，还需要保存现有的 signInNames。|否|否|输入、持久化、输出|
|state           |字符串|用户地址中的省/自治区/直辖市。 最大长度为 128。|是|是|持久化、输出|
|streetAddress   |字符串|用户营业地点的街道地址。 最大长度为 1024。|是|是|持久化、输出|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|字符串|用户的次要电话号码，用于多重身份验证。|是|否|持久化、输出|
|strongAuthenticationEmailAddress<sup>1</sup>|字符串|用户的 SMTP 地址。 示例：“bob@contoso.com”。此属性在通过用户名策略进行登录时用于存储用户电子邮件地址。 然后，该电子邮件地址将在密码重置流中使用。|是|否|持久化、输出|
|strongAuthenticationPhoneNumber<sup>1</sup>|字符串|用户的主要电话号码，用于多重身份验证。|是|否|持久化、输出|
|surname         |字符串|用户的姓（家族名或姓氏）。 最大长度为 64。|是|是|持久化、输出|
|telephoneNumber（businessPhones 的第一个条目）|字符串|用户营业地点的主要电话号码。|是|否|持久化、输出|
|userPrincipalName    |字符串|用户的用户主体名称 (UPN)。 UPN 是用户的 Internet 样式登录名，基于 Internet 标准 RFC 822。 域必须存在于租户的已验证域集合中。 创建帐户时必须使用此属性。 不可变。|否|否|输入、持久化、输出|
|usageLocation   |字符串|这对由于法律要求要向其分配许可证的用户而言为必填项，以检查服务在国家/地区中的可用性。 不可为 Null。 双字母国家/地区代码（ISO 标准 3166）。 示例：示例:“US”、“JP”和“GB”。|是|否|持久化、输出|
|userType        |字符串|一个字符串值，可用于分类目录中的用户类型。 值必须为 Member。 只读。|只读|否|持久化、输出|
|userState (externalUserState)<sup>2</sup>|字符串|仅适用于 Azure AD B2B 帐户，指示邀请是 PendingAcceptance 还是 Accepted。|否|否|持久化、输出|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|显示 UserState 属性最新更改的时间戳。|否|否|持久化、输出|
|<sup>1 </sup>不受 Microsoft Graph 支持<br><sup>2 </sup>不应该与 Azure AD B2C 一起使用||||||


## <a name="extension-attributes"></a>扩展属性

你经常需要创建自己的属性，如以下情况所示：

- 面向客户的应用程序需要保留如“LoyaltyNumber”等属性。
- 标识提供者具有必须保存的唯一用户标识符，例如 uniqueUserGUID。
- 自定义用户旅程需要保留用户的状态，例如“migrationStatus”。

Azure AD B2C 扩展存储在每个用户帐户中的属性集。 扩展属性[扩展](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)目录中用户对象的架构。 尽管扩展属性可以包含用户的数据，但它们只能在应用程序对象中注册。 扩展属性附加到名为 b2c-extensions-app 的应用程序。 请不要修改此应用程序，因为 Azure AD B2C 使用它来存储用户数据。 可在 Azure Active Directory 应用注册下找到此应用程序。

> [!NOTE]
> - 最多可将 100 个扩展属性写入任一用户帐户。
> - 如果删除 b2c-extensions-app 应用程序，则将删除所有用户的这些扩展属性及其包含的任何数据。
> - 如果应用程序删除了某个扩展属性，则将从所有用户帐户中删除该属性，同时会删除值。
> - 扩展属性的基础名称以“Extension_”+ 应用程序 ID +“_”+ 属性名称的格式生成。 例如，如果创建扩展属性 LoyaltyNumber，b2c-extensions-app 应用程序 ID 为 831374b3-bd50-41bf-aa54-263ec9e050fc，则基础扩展属性名称将为：extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber。 运行图形 API 查询创建或更新用户帐户时，将使用基础名称。

在架构扩展中定义属性时，支持以下数据类型：

|属性类型 |备注  |
|--------------|---------|
|Boolean    | 可能的值：“true”或“false” 。 |
|DateTime   | 必须以 ISO 8601 格式指定。 将以 UTC 格式存储。   |
|Integer    | 32 位值。               |
|字符串     | 最多 256 个字符。     |

## <a name="next-steps"></a>后续步骤
了解有关扩展属性的详细信息：
- [架构扩展](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [使用用户流定义自定义属性](user-flow-custom-attributes.md)
- [使用自定义策略定义自定义属性](custom-policy-custom-attributes.md)
