---
title: 定义采用 Azure Active Directory B2C 的自定义策略的验证技术配置文件 | Microsoft Docs
description: 在 Azure Active Directory B2C 的自定义策略中定义 Azure Active Directory 技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f20c3c6d6821b5a8bbdb74101095431f6f7f18f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511911"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定义采用 Azure Active Directory B2C 的自定义策略的验证技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

验证技术配置文件是来自任何协议（如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md)）的普通技术配置文件。 验证技术配置文件返回输出声明，或返回 HTTP 409 错误消息（冲突响应状态代码），其中包含以下数据：

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

从验证技术配置文件返回的声明会添加回声明包。 可以在后续验证技术配置文件中使用这些声明。

验证技术配置文件按它们在 ValidationTechnicalProfiles  元素中出现的顺序进行执行。 可以在验证技术配置文件中配置在验证技术配置文件引发错误或成功时，任何后续验证技术配置文件是否应继续执行。

验证技术配置文件可以基于 ValidationTechnicalProfile  元素中定义的前置条件有条件地执行。 例如，可以检查是否存在特定声明，或声明是否等于指定值。

自断言技术配置文件可以定义要用于验证其部分或所有输出声明的验证技术配置文件。 被引用技术配置文件的所有输入声明都必须出现在引用验证技术配置文件的输出声明中。

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

ValidationTechnicalProfiles  元素包含下列元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 要用于验证引用技术配置文件的部分或所有输出声明的技术配置文件。 |

**ValidationTechnicalProfile** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| ReferenceId | 是 | 已在策略或父策略中定义的技术配置文件的标识符。 |
|ContinueOnError|否| 指示在此验证技术配置文件引发错误时，任何后续验证技术配置文件是否应继续进行验证。 可能的值：`true` 或 `false`（默认值，进一步验证配置文件的处理会停止，并且返回错误）。 |
|ContinueOnSuccess | 否 | 指示在此验证技术配置文件成功时，任何后续验证配置文件是否应继续进行验证。 可能的值：`true` 或 `false`。 默认值是 `true`，表示进一步验证配置文件的处理会继续进行。 |

ValidationTechnicalProfile  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | 为执行验证技术配置文件而必须满足执行的前置条件的列表。 |

Precondition  元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| `Type` | 是 | 要对前置条件执行的检查或查询的类型。 指定 `ClaimsExist` 以确保在用户当前声明集中存在指定声明时应执行操作，或指定 `ClaimEquals` 以便仅当指定声明存在且其值等于指定值时才应执行操作。 |
| `ExecuteActionsIf` | 是 | 指示在测试为 true 或 false 时是否应执行前置条件中的操作。 |

Precondition  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| 值 | 1:n | 检查使用的数据。 如果此检查的类型是 `ClaimsExist`，则此字段指定要进行查询的 ClaimTypeReferenceId。 如果检查的类型是 `ClaimEquals`，则此字段指定要进行查询的 ClaimTypeReferenceId。 而另一个值元素包含要检查的值。|
| 操作 | 1:1 | 在业务流程步骤中的前置条件检查为 true 时应执行的操作。 Action  的值设置为 `SkipThisValidationTechnicalProfile`。 指定不应执行关联的验证技术配置文件。 |

### <a name="example"></a>示例

下面的示例使用这些验证技术配置文件：

1. 第一个验证技术配置文件检查用户凭据，在发生错误（如用户名无效或密码错误）时不会继续。
2. 如果 userType 声明不存在，或 userType 的值为 `Partner`，则下一个验证技术配置文件不会执行。 验证技术配置文件会尝试从内部客户数据库读取用户配置文件，并在发生错误（如 REST API 服务不可用或任何内部错误）时继续。
3. 如果 userType 声明不存在，或 userType 的值为 `Customer`，则最后一个验证技术配置文件不会执行。 验证技术配置文件会尝试从内部合作伙伴数据库读取用户配置文件，并在发生错误（如 REST API 服务不可用或任何内部错误）时继续。

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
