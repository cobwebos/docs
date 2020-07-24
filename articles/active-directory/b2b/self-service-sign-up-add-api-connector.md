---
title: 将 API 连接器添加到自助服务注册流-Azure AD
description: 配置要在用户流中使用的 web API。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6238e89b3941668f831f3128bb0e723a4097e48
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027506"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>向用户流添加 API 连接器

若要使用[api 连接器](api-connectors-overview.md)，首先要创建 api 连接器，然后在用户流中启用它。

## <a name="create-an-api-connector"></a>创建 API 连接器

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择 "**所有 api 连接器（预览版）**"，然后选择 "**新建 api 连接器**"。

   ![添加新的 API 连接器](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 为呼叫提供显示名称。 例如，**检查批准状态**。
6. 提供 API 调用的**终结点 URL** 。
7. 提供 API 的身份验证信息。

   - 目前仅支持基本身份验证。 如果希望出于开发目的使用没有基本身份验证的 API，只需输入 API 可以忽略的虚拟**用户名**和**密码**。 若要将 Azure 函数与 API 密钥一起使用，可以将代码作为查询参数包含在**终结点 URL**中（例如，https： []() //contoso.azurewebsites.net/api/endpoint<b>？ code = 0123456789</b>）。

   ![添加新的 API 连接器](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. 选择要发送到 API 的声明。
9. 选择你计划从 API 接收回的任何声明。

   ![设置 API 连接器声明](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. 选择“保存” 。

## <a name="enable-the-api-connector-in-a-user-flow"></a>在用户流中启用 API 连接器

按照以下步骤将 API 连接器添加到自助服务注册用户流。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择 "**用户流（预览版）**"，然后选择要向其添加 API 连接器的用户流。
5. 选择 " **api 连接器**"，然后选择要在用户流中的以下步骤调用的 api 终结点：

   - **使用标识提供者登录后**
   - **创建用户之前**

   ![向用户流添加 Api](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. 选择“保存” 。

了解[可在用户流中启用 API 连接器的位置](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow)。

## <a name="request-sent-to-the-api"></a>请求已发送到 API

API 连接器具体化为 HTTP POST 请求，以 JSON 正文形式发送所选声明作为键值对。 响应还应具有 HTTP 标头 `Content-Type: application/json` 。 特性的序列化与 Microsoft Graph 用户特性类似。 <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>示例请求

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
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

默认情况下，在所有请求中发送**UI 区域设置（"ui_locales"）** 声明。 它提供用户的区域设置，API 可使用它来返回国际化响应。 它不会显示在 API 配置窗格中。

如果发送的声明在调用 API 终结点时没有值，则不会将该声明发送到 API。

可以使用**extension_ \<extensions-app-id> _AttributeName**格式为用户创建自定义属性。 你的 API 应该会接收此相同序列化格式的声明。 API 可以返回带有或不包含的声明 `<extensions-app-id>` 。 有关自定义属性的详细信息，请参阅[定义自助服务注册流的自定义属性](user-flow-add-custom-attributes.md)。

> [!TIP] 
> [**标识（"标识"）**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0)和**电子邮件地址（"电子邮件"）** 声明可用于在用户拥有租户中的帐户之前标识该用户。 当用户使用 Google 或 Facebook 进行身份验证时，将发送 "标识" 声明，并且始终发送 "email"。

## <a name="expected-response-types-from-the-web-api"></a>Web API 中的预期响应类型

当 web API 在用户流中从 Azure AD 收到 HTTP 请求时，它可以返回以下响应：

- [继续响应](#continuation-response)
- [阻止响应](#blocking-response)
- [验证-错误响应](#validation-error-response)

### <a name="continuation-response"></a>继续响应

继续响应指示用户流应继续下一步。 在继续响应中，API 可以返回声明。

如果由 API 返回声明，并将其选择为**要接收的声明**，则声明会执行以下操作：

- 如果在显示页面之前调用 API 连接器，则预先填充 "属性集合" 页中的输入字段。
- 重写已分配给声明的任何值。
- 如果声明以前为 null，则为其分配一个值。

#### <a name="example-of-a-continuation-response"></a>继续响应示例

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
| 版本                                            | 字符串            | 是      | API 的版本。                                                                                                                                                                                                                                                                |
| 操作                                             | 字符串            | 是      | 值必须是 `Continue`。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 否       | 如果值被选为要在 API 连接器配置中**接收的声明**，则这些值可以存储在目录中，并可存储在用户流的**用户属性**中。 如果选择作为**应用程序声明**，则可以在令牌中返回值。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | 否       | 返回的声明不需要包含 `_<extensions-app-id>_` 。 如果值被选为要在 API 连接器配置和用户流的**用户属性**中**接收的声明**，则这些值将存储在目录中。 自定义属性不能在令牌中发回。 |

### <a name="blocking-response"></a>阻止响应

阻止响应会退出用户流。 它可以由 API 有意颁发，通过向用户显示块页来停止用户流的继续。 "块" 页显示 `userMessage` API 提供的。

阻塞响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| 参数   | 类型   | 必须 | 说明                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| 版本     | 字符串 | 是      | API 的版本。                                                    |
| 操作      | 字符串 | 是      | 值必须是 `ShowBlockPage`                                              |
| userMessage | 字符串 | 是      | 要向用户显示的消息。                                            |
| code        | 字符串 | 否       | 错误代码。 可用于调试目的。 不会向用户显示。 |

#### <a name="end-user-experience-with-a-blocking-response"></a>阻止响应的最终用户体验

![示例块页](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>验证-错误响应

在属性集合页后调用的 API 调用可能会返回验证错误响应。 当这样做时，用户流将停留在 "属性集合" 页上，并 `userMessage` 向用户显示。 然后，用户可以编辑和重新提交表单。 这种类型的响应可用于输入验证。

#### <a name="example-of-a-validation-error-response"></a>验证错误响应示例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| 参数   | 类型    | 必须 | 说明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| 版本     | 字符串  | 是      | API 的版本。                                                    |
| 操作      | 字符串  | 是      | 值必须是 `ValidationError`。                                           |
| status      | Integer | 是      | 必须是 `400` ValidationError 响应的值。                        |
| userMessage | 字符串  | 是      | 要向用户显示的消息。                                            |
| code        | 字符串  | 否       | 错误代码。 可用于调试目的。 不会向用户显示。 |

#### <a name="end-user-experience-with-a-validation-error-response"></a>验证-错误响应的最终用户体验

!["验证" 页示例](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>与 Azure Functions 集成
可以在 Azure Functions 中使用 HTTP 触发器，作为创建用于 API 连接器的 API 的简单方式。 [例如](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)，使用 Azure 函数执行验证逻辑，并限制对特定域的登录。 还可以调用和调用其他 web Api、用户存储和其他云服务。

## <a name="next-steps"></a>后续步骤

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- 了解如何[将自定义审批工作流添加到自助注册](self-service-sign-up-add-approvals.md)
- [Azure Function 快速入门示例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)入门。
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
