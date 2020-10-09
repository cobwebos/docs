---
title: 向用户流添加 API 连接器
description: 配置要在用户流中使用的 API 连接器。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 09/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 092246da4c01aa826fd4425e5a778d567b75d9d6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828338"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>将 API 连接器添加到注册用户流

若要使用 [api 连接器](api-connectors-overview.md)，首先要创建 api 连接器，然后在用户流中启用它。

## <a name="create-an-api-connector"></a>创建 API 连接器

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在 " **Azure 服务**" 下，选择 **Azure AD B2C**。
4. 选择 " **API 连接器 (预览") **，然后选择 " **新建 api 连接器**"。

   ![添加新的 API 连接器](./media/add-api-connector/api-connector-new.png)

5. 为呼叫提供显示名称。 例如， **验证用户信息**。
6. 提供 API 调用的 **终结点 URL** 。
7. 提供 API 的身份验证信息。

   - 目前仅支持基本身份验证。 如果希望出于开发目的使用没有基本身份验证的 API，只需输入 API 可以忽略的 "虚拟" **用户名** 和 **密码** 。 若要将 Azure 函数与 API 密钥一起使用，可以将代码作为查询参数包含在 **终结点 URL** 中 (例如，https： []() //contoso.azurewebsites.net/api/endpoint<b>？ code = 0123456789</b>) 。

   ![配置新的 API 连接器](./media/add-api-connector/api-connector-config.png)
8. 选择“保存”。

## <a name="the-request-sent-to-your-api"></a>发送到 API 的请求
API 连接器具体化为 **HTTP POST** 请求，发送 ( "声明" ) 为 JSON 正文中的键值对的用户属性。 特性的序列化与 [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user#properties) 用户属性类似。 

**示例请求**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

只有**Azure Active Directory**  >  **External 标识**  >  **自定义用户属性**中列出的用户属性和自定义属性可在请求中发送。

自定义属性在目录的 **extension_ \<extensions-app-id> _CustomAttribute**  格式中存在。 你的 API 应该会接收此相同序列化格式的声明。 有关自定义属性的详细信息，请参阅 [在 Azure Active Directory B2C 中定义自定义属性](user-flow-custom-attributes.md)。

此外，默认情况下，在所有请求中发送 ** ( "ui_locales" ) 声明的 UI 区域设置 ** 。 它提供在其设备上配置的用户区域设置 () ，API 可以使用此区域设置来返回国际化响应。

> [!IMPORTANT]
> 如果调用 API 终结点时声明没有值，则不会将该声明发送到 API。 应将 API 设计为显式检查并处理声明不在请求中的情况。

> [!TIP] 
> [**标识 ( "标识" ) **](https://docs.microsoft.com/graph/api/resources/objectidentity) 并且 **电子邮件地址 ( "email" ) ** 声明在用户拥有租户中的帐户之前，你的 API 可以使用它来标识该用户。 当用户使用标识提供程序（如 Google 或 Facebook）进行身份验证时，将发送 "标识" 声明。 始终发送 "email"。

## <a name="enable-the-api-connector-in-a-user-flow"></a>在用户流中启用 API 连接器

按照以下步骤将 API 连接器添加到注册用户流。

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在 " **Azure 服务**" 下，选择 **Azure AD B2C**。
4. 选择 " **用户流**"，然后选择要向其添加 API 连接器的用户流。
5. 选择 " **api 连接器**"，然后选择要在用户流中的以下步骤调用的 api 终结点：

   - **使用标识提供者登录后**
   - **创建用户之前**

   ![向用户流添加 Api](./media/add-api-connector/api-connectors-user-flow-select.png)

6. 选择“保存”。

## <a name="after-signing-in-with-an-identity-provider"></a>使用标识提供者登录后

在用户使用标识 (提供者（如 Google、Facebook、& Azure AD) 进行身份验证后，会立即调用注册过程中的 API 连接器。 此步骤优先于 " ***属性集合" 页***，它是向用户显示的用于收集用户属性的窗体。 如果用户正在使用本地帐户注册，则不会调用此步骤。

### <a name="example-request-sent-to-the-api-at-this-step"></a>此步骤发送到 API 的示例请求
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

发送到 API 的确切声明取决于标识提供者提供的信息。 始终发送 "email"。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>此步骤中的 web API 所需的响应类型

当 web API 在用户流中从 Azure AD 收到 HTTP 请求时，它可以返回以下响应：

- 继续响应
- 阻止响应

#### <a name="continuation-response"></a>继续响应

继续响应指示用户流应继续执行下一步： "属性集合" 页。

在继续响应中，API 可以返回声明。 如果 API 返回声明，则声明会执行以下操作：

- 预先填充 "属性集合" 页中的输入字段。

请参阅 [继续响应](#example-of-a-continuation-response)的示例。

#### <a name="blocking-response"></a>阻止响应

阻止响应会退出用户流。 它可以由 API 有意颁发，通过向用户显示块页来停止用户流的继续。 "块" 页显示 `userMessage` API 提供的。

查看 [阻塞响应](#example-of-a-blocking-response)的示例。

## <a name="before-creating-the-user"></a>创建用户之前

如果包含 "属性集合" 页，则在注册过程中的此步骤调用 API 连接器。 始终在创建用户帐户之前调用此步骤。

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>此步骤发送到 API 的示例请求

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
发送到 API 的确切声明取决于从用户收集的信息，或由标识提供者提供的信息。

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>此步骤中的 web API 所需的响应类型

当 web API 在用户流中从 Azure AD 收到 HTTP 请求时，它可以返回以下响应：

- 继续响应
- 阻止响应
- 验证响应

#### <a name="continuation-response"></a>继续响应

继续响应表示用户流应继续执行下一步：在目录中创建用户。

在继续响应中，API 可以返回声明。 如果 API 返回声明，则声明会执行以下操作：

- 从 "属性集合" 页重写已分配给声明的任何值。

请参阅 [继续响应](#example-of-a-continuation-response)的示例。

#### <a name="blocking-response"></a>阻止响应
阻止响应会退出用户流。 它可以由 API 有意颁发，通过向用户显示块页来停止用户流的继续。 "块" 页显示 `userMessage` API 提供的。

查看 [阻塞响应](#example-of-a-blocking-response)的示例。

### <a name="validation-error-response"></a>验证-错误响应
 当 API 使用验证错误响应进行响应时，用户流将停留在 "属性集合" 页上，并 `userMessage` 向用户显示。 然后，用户可以编辑和重新提交表单。 这种类型的响应可用于输入验证。

查看 [验证错误响应](#example-of-a-validation-error-response)的示例。

## <a name="example-responses"></a>示例响应

### <a name="example-of-a-continuation-response"></a>继续响应示例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 参数                                          | 类型              | 必须 | 说明                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 版本                                            | String            | 是      | API 的版本。                                                                                                                                                                                                                                                                |
| action                                             | String            | 是      | 值必须是 `Continue`。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 否       | 如果值被选为要在 API 连接器配置中 **接收的声明** ，则这些值可以存储在目录中，并可存储在用户流的 **用户属性** 中。 如果选择作为 **应用程序声明**，则可以在令牌中返回值。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 返回的声明不需要包含 `_<extensions-app-id>_` 。 如果值被选为要在 API 连接器配置和用户流的**用户属性**中**接收的声明**，则这些值将存储在目录中。 自定义属性不能在令牌中发回。 |

### <a name="example-of-a-blocking-response"></a>阻塞响应的示例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| 参数   | 类型   | 必须 | 说明                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 版本     | String | 是      | API 的版本。                                                    |
| action      | String | 是      | 值必须是 `ShowBlockPage`                                              |
| userMessage | String | 是      | 要向用户显示的消息。                                            |

**阻止响应的最终用户体验**

![示例块页](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>验证错误响应示例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| 参数   | 类型    | 必须 | 说明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 版本     | String  | 是      | API 的版本。                                                    |
| action      | String  | 是      | 值必须是 `ValidationError`。                                           |
| status      | Integer | 是      | 必须是 `400` ValidationError 响应的值。                        |
| userMessage | String  | 是      | 要向用户显示的消息。                                            |

**验证-错误响应的最终用户体验**

!["验证" 页示例](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>最佳做法和故障排除方法

### <a name="using-serverless-cloud-functions"></a>使用无服务器云功能
无服务器函数（如 Azure Functions 中的 HTTP 触发器）提供了一种简单的方法来创建 API 终结点，以便与 API 连接器一起使用。 例如，你可以使用无服务器云功能来执行验证逻辑，并限制对特定域的登录。 无服务器云功能还可以调用和调用其他 web Api、用户存储和其他云服务，以实现更复杂的方案。

### <a name="best-practices"></a>最佳做法
请确保：
* API 遵循上述 API 请求和响应约定。 
* API 连接器的 **终结点 URL** 指向正确的 API 终结点。
* API 显式检查收到的声明的 null 值。
* 你的 API 可以尽快做出响应，以确保具有流畅的用户体验。
    * 如果使用无服务器函数或可缩放的 web 服务，请使用保持 API "唤醒" 或 "热" 的托管计划。 在生产环境中。 对于 Azure Functions，建议使用 [高级计划](../azure-functions/functions-scale.md)


### <a name="use-logging"></a>使用日志记录
通常，使用由 web API 服务（如 [Application insights](../azure-functions/functions-monitoring.md)）启用的日志记录工具来监视 API 是否存在意外错误代码、异常和性能不佳的情况。
* 监视 http 200 或400以外的 HTTP 状态代码。
* 401或 403 HTTP 状态代码通常指示身份验证存在问题。 仔细检查 api 的身份验证层和 API 连接器中的相应配置。
* 如果需要，请使用更严格的日志记录级别 (例如 "跟踪" 或 "调试" ) 。
* 监视 API 长时间响应。

## <a name="next-steps"></a>后续步骤
<!-- - Learn how to [add a custom approval workflow to sign-up](add-approvals.md) -->
<!-- - Get started with our [Azure Function quickstart samples](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts). -->
