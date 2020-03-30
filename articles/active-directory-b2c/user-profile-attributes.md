---
title: Azure 活动目录 B2C 中的用户配置文件属性
description: 了解 Azure AD B2C 目录用户配置文件支持的用户资源类型属性。 了解内置属性、扩展以及属性如何映射到 Microsoft 图形。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057289"
---
# <a name="user-profile-attributes"></a>用户配置文件属性

Azure 活动目录 （Azure AD） B2C 目录用户配置文件附带一组内置属性，如给定名称、姓氏、城市、邮政编码和电话号码。 您可以使用自己的应用程序数据扩展用户配置文件，而无需外部数据存储。 大多数可用于 Azure AD B2C 用户配置文件的属性也受 Microsoft Graph 的支持。 本文介绍了支持的 Azure AD B2C 用户配置文件属性。 它还注意到 Microsoft 图形不支持的属性，以及不应与 Azure AD B2C 一起使用的 Microsoft 图形属性。

> [!IMPORTANT]
> 您不应使用内置或扩展属性来存储敏感的个人数据，例如帐户凭据、政府标识号、持卡人数据、财务帐号数据、医疗保健信息或敏感背景信息。

您还可以与外部系统集成。 例如，可以使用 Azure AD B2C 进行身份验证，但委托给外部客户关系管理 （CRM） 或客户忠诚度数据库作为客户数据源的权威来源。 有关详细信息，请参阅[远程配置文件](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解决方案。

下表列出了 Azure AD B2C 目录用户配置文件支持[的用户资源类型](https://docs.microsoft.com/graph/api/resources/user)属性。 它提供有关每个属性的以下信息：

- Azure AD B2C 使用的属性名称（后跟括号中的 Microsoft 图形名称（如果不同）
- 属性数据类型
- 属性描述
- 如果属性在 Azure 门户中可用
- 如果属性可以在用户流中使用
- 如果属性可以在自定义策略 Azure AD[技术配置文件](active-directory-technical-profile.md)中使用，并在哪个部分（&lt;输入声明&gt;、&lt;输出声明&gt;或&lt;持久声明&gt;）中使用。

|“属性”     |类型     |说明|Azure 门户|用户流|自定义策略|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|用户帐户是启用还是禁用：如果启用了该帐户，**为 true，** 否则**为 false**。|是|否|持久，输出|
|ageGroup        |String|用户的年龄组。 可能的值：空、未定义、次要、成人、非成人。|是|否|持久，输出|
|替代安全Id （[标识](manage-user-accounts-graph-api.md#identities-property)）|String|来自外部标识提供程序的单个用户标识。|否|否|输入、持久、输出|
|替代安全 Id （[标识](manage-user-accounts-graph-api.md#identities-property)）|替代安全 Id 集合|来自外部标识提供程序的用户标识的集合。|否|否|持久，输出|
|city            |String|用户所在的城市。 最大长度 128。|是|是|持久，输出|
|同意为次要提供|String|是否为未成年人提供了同意。 允许的值：null、授予、拒绝或不需要。|是|否|持久，输出|
|country         |String|用户所在的国家/地区。 示例："美国"或"英国"。 最大长度 128。|是|是|持久，输出|
|createdDateTime|DateTime|创建用户对象的日期。 只读。|否|否|持久，输出|
|creationType    |String|如果用户帐户是作为 Azure 活动目录 B2C 租户的本地帐户创建的，则该值为本地帐户或名称"共存"。 只读。|否|否|持久，输出|
|dateOfBirth     |Date|出生日期。|否|否|持久，输出|
|department      |String|用户工作所在部门的名称。 最大长度 64。|是|否|持久，输出|
|displayName     |String|用户的显示名称。 最大长度 256。|是|是|持久，输出|
|传真电话<sup>1</sup>|String|用户的业务传真机的电话号码。|是|否|持久，输出|
|givenName       |String|用户的给定名称（名字）。 最大长度 64。|是|是|持久，输出|
|jobTitle        |String|用户的职务。 最大长度 128。|是|是|持久，输出|
|immutableId     |String|通常用于从本地活动目录迁移的用户的标识符。|否|否|持久，输出|
|法律年龄组分类|String|法定年龄组分类。 只读，并根据年龄组和同意为次要属性计算。 允许的值：空、未成年人、父母同意、未成年人家长同意、未成年人无父母同意，非成人和成人。|是|否|持久，输出|
|法律国家<sup>1</sup>  |String|国家/地区用于法律目的。|否|否|持久，输出|
|mail            |String|用户的 SMTP 地址，例如""。bob@contoso.com 只读。|否|否|持久，输出|
|mailNickName    |String|用户的邮件别名。 最大长度 64。|否|否|持久，输出|
|手机（手机） |String|用户的主移动电话号码。 最大长度 64。|是|否|持久，输出|
|netId           |String|净 ID。|否|否|持久，输出|
|objectId        |String|一个全局唯一标识符 （GUID），它是用户的唯一标识符。 示例：12345678-9abc-def0-1234-56789abcde。 只读，不可改变。|只读|是|输入、持久、输出|
|otherMails      |字符串集合|用户的其他电子邮件地址列表。 示例：["，"""*。bob@contoso.comRobert@fabrikam.com|是（备用电子邮件）|否|持久，输出|
|password        |String|用户创建期间本地帐户的密码。|否|否|Persisted|
|passwordPolicies     |String|密码策略。 它是一个由用逗号分隔的不同策略名称组成的字符串。 即"禁用密码过期，禁用强密码"。|否|否|持久，输出|
|物理交付办公室名称（办公地点）|String|用户营业场所的办公地点。 最大长度 128。|是|否|持久，输出|
|postalCode      |String|用户通信地址的邮政编码。 邮政编码特定于用户的国家/地区。 在美国，此属性包含邮政编码。 最大长度 40。|是|否|持久，输出|
|preferredLanguage    |String|用户的首选语言。 应遵循 ISO 639-1 规范。 示例："en-US"。|否|否|持久，输出|
|刷新令牌从日期时间有效|DateTime|在此时间之前发出的任何刷新令牌都无效，并且应用程序在使用无效刷新令牌获取新的访问令牌时将收到错误。 如果发生这种情况，应用程序将需要通过向授权终结点发出请求来获取新的刷新令牌。 只读。|否|否|输出|
|符号名称 （[标识](manage-user-accounts-graph-api.md#identities-property)） |String|目录中任何类型的本地帐户用户的唯一登录名。 使用此选项可获取具有登录值的用户，而不指定本地帐户类型。|否|否|输入|
|符号名称.用户名 （[标识](manage-user-accounts-graph-api.md#identities-property)） |String|目录中本地帐户用户的唯一用户名。 使用此选项可以创建或使用特定登录用户名的用户。 在修补程序操作期间仅在持久声明中指定此名称将删除其他类型的符号 InNames。 如果要添加新类型的符号 InNames，还需要保留现有符号 InNames。|否|否|输入、持久、输出|
|符号名称.电话号码 （[标识](manage-user-accounts-graph-api.md#identities-property)） |String|目录中本地帐户用户的唯一电话号码。 使用此选项可以创建或获取具有特定登录电话号码的用户。 在修补程序操作期间仅在持久声明中指定此名称将删除其他类型的符号 InNames。 如果要添加新类型的符号 InNames，还需要保留现有符号 InNames。|否|否|输入、持久、输出|
|符号名称.电子邮件地址 （[身份](manage-user-accounts-graph-api.md#identities-property)）|String|目录中本地帐户用户的唯一电子邮件地址。 使用此选项可以创建或使用特定登录电子邮件地址的用户。 在修补程序操作期间仅在持久声明中指定此名称将删除其他类型的符号 InNames。 如果要添加新类型的符号 InNames，还需要保留现有符号 InNames。|否|否|输入、持久、输出|
|state           |String|用户地址中的省/市/自治区。 最大长度 128。|是|是|持久，输出|
|streetAddress   |String|用户营业场所的街道地址。 最大长度 1024。|是|是|持久，输出|
|强身份验证替代电话编号<sup>1</sup>|String|用户的第二个电话号码，用于多重身份验证。|是|否|持久，输出|
|强身份验证电子邮件地址<sup>1</sup>|String|用户的 SMTP 地址。 示例："bob@contoso.com此属性用于使用用户名策略登录，以存储用户电子邮件地址。 然后，在密码重置流中使用的电子邮件地址。|是|否|持久，输出|
|强<sup>身份验证电话</sup>1|String|用户的主要电话号码，用于多重身份验证。|是|否|持久，输出|
|surname         |String|用户的姓氏（家族名称或姓）。 最大长度 64。|是|是|持久，输出|
|电话号码（商务电话首次输入）|String|用户营业场所的主电话号码。|是|否|持久，输出|
|userPrincipalName    |String|用户的用户主体名称 (UPN)。 UPN 是用户的 Internet 样式登录名，基于 Internet 标准 RFC 822。 域必须存在于租户的已验证域集合中。 创建帐户时需要此属性。 不可变：|否|否|输入、持久、输出|
|usageLocation   |String|对于根据法律要求需向其分配许可证的用户，这是检查服务在相应国家/地区的可用性所必需的。 不可为 Null。 双字母国家/地区代码（ISO 标准 3166）。 示例："美国"、"JP"和"GB"。|是|否|持久，输出|
|userType        |String|可用于对目录中的用户类型进行分类的字符串值。 值必须为"成员"。 只读。|只读|否|持久，输出|
|用户状态（外部用户状态）<sup>2</sup>|String|仅对于 Azure AD B2B 帐户，指示邀请是"待定接受"还是"已接受"。|否|否|持久，输出|
|用户状态更改 On（外部用户状态更改日期时间）<sup>2</sup>|DateTime|显示用户状态属性的最新更改的时间戳。|否|否|持久，输出|
|<sup>1</sup>微软图形不支持<br><sup>2</sup>不应与 Azure AD B2C 一起使用||||||


## <a name="extension-attributes"></a>扩展属性

您通常需要创建自己的属性，如以下情况：

- 面向客户的应用程序需要保留像 **"忠诚号**"这样的属性。
- 标识提供者具有必须保存的唯一用户标识符，例如 uniqueUserGUID****。
- 自定义用户旅程需要保留用户的状态，如**迁移状态**。

Azure AD B2C 扩展存储在每个用户帐户中的属性集。 扩展属性[扩展](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)目录中用户对象的架构。 扩展属性只能在应用程序对象上注册，即使它们可能包含用户的数据。 扩展属性附加到称为 b2c 扩展应用程序的应用程序。 不要修改此应用程序，因为它被 Azure AD B2C 用于存储用户数据。 您可以在 Azure 活动目录应用注册下找到此应用程序。

> [!NOTE]
> - 最多可以将 100 个扩展属性写入任何用户帐户。
> - 如果删除了 b2c 扩展应用程序，则这些扩展属性将连同包含的任何数据从所有用户中删除。
> - 如果应用程序删除了扩展属性，它将从所有用户帐户中删除，并且删除这些值。
> - 扩展属性的基础名称以"Extension_" + 应用程序 ID = "*" = 属性名称的形式生成。 例如，如果创建扩展属性"忠诚编号"，并且 b2c 扩展应用程序应用程序 ID 为 831374b3-bd50-41bf-aa54-263ec9e050fc，则基础扩展属性名称将为：extension_831374b3bd5041bfaa54263ec9e050fc_忠诚度号码。 在运行图形 API 查询时使用基础名称来创建或更新用户帐户。

在架构扩展中定义属性时，支持以下数据类型：

|属性类型 |备注  |
|--------------|---------|
|Boolean    | 可能的值：**真**或**假**。 |
|DateTime   | 必须以 ISO 8601 格式指定。 将以 UTC 格式存储。   |
|Integer    | 32 位值。               |
|String     | 最多 256 个字符。     |

## <a name="next-steps"></a>后续步骤
了解有关扩展属性的更多详细信息：
- [架构扩展](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [使用用户流定义自定义属性](user-flow-custom-attributes.md)
- [使用自定义策略定义自定义属性](custom-policy-custom-attributes.md)
