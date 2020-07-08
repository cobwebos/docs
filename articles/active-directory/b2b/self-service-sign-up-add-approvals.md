---
title: 将自定义审批添加到自助服务注册流-Azure AD
description: 在 "外部标识" 自助注册-Azure Active Directory （Azure AD）中为自定义审批工作流添加 API 连接器
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
ms.openlocfilehash: c4b40c284c8d034d92f29eb25d754d9294ac2e3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386770"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>将自定义审批工作流添加到自助注册

利用[API 连接器](api-connectors-overview.md)，你可以将自己的自定义批准工作流与自助注册集成，以便你可以管理在你的租户中创建的来宾用户帐户。

本文提供了有关如何与审批系统集成的示例。 在此示例中，自助注册用户流会在注册过程中收集用户数据，并将其传递给你的审批系统。 然后，批准系统可以：

- 自动批准用户并允许 Azure AD 创建用户帐户。
- 触发手动审阅。 如果请求得到批准，批准系统将使用 Microsoft Graph 来预配用户帐户。 审批系统还可以通知用户已创建其帐户。

## <a name="register-an-application-for-your-approval-system"></a>为审批系统注册应用程序

你需要将你的审批系统注册为 Azure AD 租户中的应用程序，以便它可以使用 Azure AD 进行身份验证，并且具有创建用户的权限。 详细了解[Microsoft Graph 的身份验证和授权基础知识](https://docs.microsoft.com/graph/auth/auth-concepts)。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择 "**应用注册**"，然后选择 "**新注册**"。
4. 输入应用程序的**名称**，例如 "_注册审批_"。

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. 选择“注册”。 可以将其他字段保留默认值。

   ![注册应用程序页面](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. 在左侧菜单中的 "**管理**" 下，选择 " **API 权限**"，然后选择 "**添加权限**"。
7. 在 "**请求 API 权限**" 页上，选择 " **Microsoft Graph**"，然后选择 "**应用程序权限**"。
8. 在 "**选择权限**" 下，展开 "**用户**"，然后选择 "**用户**"。 此权限允许批准系统在批准时创建用户。 然后选择“添加权限”。

   ![注册应用程序页面](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. 在 " **API 权限**" 页上，选择 "**授予管理员同意（你的租户名称）**"，然后选择 **"是"**。
10. 在左侧菜单中的 "**管理**" 下，选择 "**证书 & 密码**"，然后选择 "**新建客户端密码**"。
11. 输入机密的**说明**（例如，_批准客户端机密_），并选择客户端密钥**过期**的持续时间。 然后选择“添加”。
12. 复制 "客户端密钥" 的值。

    ![复制要在批准系统中使用的客户端机密](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. 将你的审批系统配置为使用**应用程序 id**作为客户端 id，并将生成的**客户端密码**用于 Azure AD 进行身份验证。

## <a name="create-the-api-connectors"></a>创建 API 连接器

接下来，你将为自助注册用户流[创建 API 连接器](self-service-sign-up-add-api-connector.md#create-an-api-connector)。 你的审批系统 API 需要两个连接器和对应的终结点，如下面所示的示例。 这些 API 连接器执行以下操作：

- **检查审批状态**。 用户使用标识提供者登录后立即向审批系统发送调用，以检查用户是否具有现有审批请求或已被拒绝。 如果批准系统仅执行自动批准决策，则可能不需要此 API 连接器。 下面是 "检查批准状态" API 连接器的示例。

  ![检查审批状态 API 连接器配置](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **请求批准**-在用户完成 "属性收集" 页后，但在创建用户帐户之前，向审批系统发送调用以请求批准。 可以自动授予或手动查看批准请求。 下面是 "请求批准" API 连接器的示例。 选择**要发送**的任何声明，批准系统需要做出批准决定。

  ![请求审批 API 连接器配置](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

若要创建这些连接器，请按照[创建 API 连接器](self-service-sign-up-add-api-connector.md#create-an-api-connector)中的步骤操作。

## <a name="enable-the-api-connectors-in-a-user-flow"></a>在用户流中启用 API 连接器

现在，你将使用以下步骤将 API 连接器添加到自助注册用户流：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 选择 "**用户流（预览版）**"，然后选择要为其启用 API 连接器的用户流。
5. 选择 " **api 连接器**"，然后选择要在用户流中的以下步骤调用的 api 终结点：

   - **使用标识提供者登录后**：选择你的审批状态 API 连接器，例如_检查批准状态_。
   - **在创建用户之前**：选择你的审批请求 API 连接器，例如_请求批准_。

   ![向用户流添加 Api](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. 选择“保存”。

## <a name="control-the-sign-up-flow-with-api-responses"></a>用 API 响应控制注册流

你的批准系统可以使用两个 API 终结点的[api 响应类型](self-service-sign-up-add-api-connector.md#expected-response-types-from-the-web-api)来控制注册流。

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>"检查批准状态" API 连接器的请求和响应

下面是 API 从 "检查批准状态" API 连接器接收的请求的示例：

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-check-approval-status"></a>"检查批准状态" 的继续响应

如果出现以下情况，则**检查审批状态**API 终结点应返回延续响应：

- 用户以前没有请求批准。

下面是继续响应的一个示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>正在阻止 "检查批准状态" 的响应

如果出现以下情况，则**检查审批状态**API 终结点应返回一个阻止响应：

- 用户审批处于挂起状态。
- 用户已被拒绝，不应允许再次请求审批。

下面是阻止响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>请求和响应 "请求批准" API 连接器

下面是 API 从 "请求批准" API 连接器接收的 HTTP 请求的示例：

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

#### <a name="continuation-response-for-request-approval"></a>"请求批准" 的延续响应

如果出现以下情况，**请求审批**API 终结点应返回延续响应：

- 用户可以**_自动获得批准_**。

下面是继续响应的一个示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> 如果收到继续响应，Azure AD 将创建用户帐户，并将用户定向到应用程序。

#### <a name="blocking-response-for-request-approval"></a>正在阻止 "请求批准" 响应

如果出现以下情况，**请求批准**API 终结点应返回一个阻止响应：

- 已创建用户批准请求，现已将其挂起。
- 已自动拒绝用户批准请求。

下面是阻止响应的示例：

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

`userMessage`响应中的将向用户显示，例如：

![示例等待批准页](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>手动批准后创建用户帐户

获取手动批准后，自定义批准系统使用[Microsoft Graph](https://docs.microsoft.com/graph/use-the-api)创建[用户](https://docs.microsoft.com/graph/azuread-users-concept-overview)帐户。 审批系统预配用户帐户的方式取决于用户使用的标识提供者。

### <a name="for-a-federated-google-or-facebook-user"></a>对于联合 Google 或 Facebook 用户

> [!IMPORTANT]
> 批准系统应显式检查并显示和，这 `identities` `identities[0]` `identities[0].issuer` `identities[0].issuer` 等于 "facebook" 或 "google" 才能使用此方法。

如果用户使用 Google 或 Facebook 帐户登录，则可以使用[用户创建 API](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0&tabs=http)。

1. 审批系统使用将从用户流接收 HTTP 请求。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 审批系统使用 Microsoft Graph 来创建用户帐户。

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| 参数                                           | 必需 | 描述                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | 是      | 可以通过将 `email_address` 声明发送到 API，将该 `@` 字符替换 `_` 为，并预先将其挂起到来生成 `#EXT@<tenant-name>.onmicrosoft.com` 。 |
| accountEnabled                                      | 是      | 必须设置为 `true`。                                                                                                                                                 |
| mail                                                | 是      | 等效于 `email_address` 发送到 API 的声明。                                                                                                               |
| userType                                            | 是      | 必须是 `Guest`。 将此用户指定为来宾用户。                                                                                                                 |
| identities                                          | 是      | 联合标识信息。                                                                                                                                    |
| \<otherBuiltInAttribute>                            | 否       | 其他内置属性 `displayName` ，如、 `city` 和其他。 参数名称与 API 连接器发送的参数相同。                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | 否       | 有关用户的自定义属性。 参数名称与 API 连接器发送的参数相同。                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>对于联合 Azure Active Directory 用户

如果用户使用联合 Azure Active Directory 帐户登录，则必须使用[邀请 API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0)创建用户，并根据需要使用[用户更新 api](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)向用户分配更多属性。

1. 审批系统从用户流接收 HTTP 请求。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 审批系统使用 `email_address` API 连接器提供的来创建邀请。

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

下面是响应的示例：

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. 审批系统使用受邀用户的 ID 来更新用户的帐户，其中包含收集的用户特性（可选）。

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>后续步骤

- [Azure Function 快速入门示例](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)入门。
- [查看具有手动审批的来宾用户的自助注册示例](code-samples-self-service-sign-up.md#custom-approval-workflows)。 
