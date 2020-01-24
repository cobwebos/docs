---
title: Microsoft 标识平台范围、权限和同意 |Microsoft Docs
description: Microsoft 标识平台终结点中授权的说明，包括范围、权限和许可。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.openlocfilehash: 29e099e1c53f83d038caa697d11158fd5939ca7b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700305"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft 标识平台终结点中的权限和许可

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

与 Microsoft 标识平台集成的应用程序遵循的授权模型可让用户和管理员控制数据的访问方式。 授权模型的实现已在 Microsoft 标识平台终结点上更新，它更改了应用必须与 Microsoft 标识平台进行交互的方式。 本文涵盖此授权模型的基本概念，包括范围、权限和同意。

> [!NOTE]
> Microsoft 标识平台终结点并不支持所有方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="scopes-and-permissions"></a>范围和权限

Microsoft 标识平台实现 [OAuth 2.0](active-directory-v2-protocols.md) 授权协议。 OAuth 2.0 是一种方法，通过此方法，第三方应用可以代表用户访问 Web 托管资源。 与 Microsoft 标识平台集成的任何 Web 托管资源都有一个资源标识符，也称为“应用程序 ID URI”。 例如，Microsoft 的某些 Web 托管资源包括：

* Microsoft Graph：`https://graph.microsoft.com`
* Office 365 邮件 API：`https://outlook.office.com`
* Azure AD Graph：`https://graph.windows.net`
* Azure Key Vault： `https://vault.azure.net`

> [!NOTE]
> 我们强烈建议使用 Microsoft Graph，而不要使用 Azure AD Graph、Office 365 邮件 API 等。

这同样适用于已与 Microsoft 标识平台集成的任何第三方资源。 这些资源还可以定义一组可用于将该资源的功能分成较小区块的权限。 例如，[Microsoft Graph](https://graph.microsoft.com) 定义了执行以下（以及其他）任务的权限：

* 读取用户的日历
* 写入用户的日历
* 以用户身份发送邮件

通过定义这些类型的权限，资源可以更精细地控制其数据以及 API 功能的公开方式。 第三方应用可以从用户和管理员请求这些权限，只有在用户或管理员批准该请求之后，应用才能代表用户访问或处理数据。 将资源的功能切割成较小的权限集，即可将第三方应用构建为只请求执行其功能所需的特定权限。 用户和管理员可以确切了解应用有权访问的数据，并且可以更有把握地确保它不会受到恶意攻击。 开发人员应始终遵守“最低特权”的概念，仅请求分配正常运行应用程序所需的权限。

在 OAuth 2.0 中，这些类型的权限称为“范围”。 它们也通常称为 "*权限*"。 权限在 Microsoft 标识平台中以字符串值表示。 仍以 Microsoft Graph 为例，每个权限的字符串值为：

* 使用 `Calendars.Read` 读取用户的日历
* 使用 `Calendars.ReadWrite` 写入用户的日历
* 使用 `Mail.Send` 以用户身份发送邮件

应用最常通过在 Microsoft 标识平台授权终结点的请求中指定范围来请求这些权限。 但是，某些高权限权限只能通过管理员同意授予，并使用[管理员许可终结点](v2-permissions-and-consent.md#admin-restricted-permissions)请求/授予。 继续阅读，了解详细信息。

## <a name="permission-types"></a>权限类型

Microsoft 标识平台支持两种类型的权限：**委托的权限**和**应用程序权限**。

* **委托的权限**由包含登录用户的应用使用。 对于这些应用程序，用户或管理员同意应用程序所请求的权限，并且在调用目标资源时，将该应用程序作为已登录用户的权限。 某些委托的权限可由非管理用户许可，但某些更高特权的权限需要[管理员许可](v2-permissions-and-consent.md#admin-restricted-permissions)。 若要了解哪些管理员角色可以同意委托的权限，请参阅 [Azure AD 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)。

* **应用程序权限**由无需存在登录用户即可运行的应用使用；例如，以后台服务或守护程序形式运行的应用。  应用程序权限只能[由管理员许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

有效权限是应用在对目标资源发出请求时拥有的权限。 在调用目标资源时，务必要了解你的应用程序授予的委托权限和应用程序权限之间的差异，以及其有效权限。

- 对于委托的权限，应用的有效权限是（通过许可）授予应用的委托权限与当前登录用户的特权的最低特权交集。 应用的特权永远不会超过登录用户的特权。 在组织内部，可以通过策略或者一个或多个管理员角色的成员身份来确定登录用户的特权。 若要了解哪些管理员角色可以同意委托的权限，请参阅 [Azure AD 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)。

   例如，假设为应用授予了 Microsoft Graph 中的 _User.ReadWrite.All_ 委托权限。 此权限在名义上会授予应用读取和更新组织中每个用户的个人资料的权限。 如果登录用户是全局管理员，则应用可以更新组织中每个用户的个人资料。 但是，如果登录用户不是管理员角色，你的应用将只能更新已登录用户的配置文件。 它无法更新组织中其他用户的个人资料，因为该应用有权代表的用户没有这些特权。
  
- 对于应用程序权限，应用的有效权限是权限默示的完整特权级别。 例如，拥有 _User.ReadWrite.All_ 应用程序权限的应用可以更新组织中每个用户的个人资料。 

## <a name="openid-connect-scopes"></a>OpenID Connect 范围

OpenID Connect 的 Microsoft 标识平台实现具有几个明确定义的作用域，不适用于特定资源： `openid`、`email`、`profile`和 `offline_access`。 不支持 `address` 和 `phone` OpenID Connect 范围。

### <a name="openid"></a>openid

如果应用使用 [OpenID Connect](active-directory-v2-protocols.md) 执行登录，则必须请求 `openid` 范围。 `openid` 范围在工作帐户同意页上显示为“登录”权限，而在个人 Microsoft 帐户同意页上显示为“查看配置文件并使用 Microsoft 帐户连接到应用和服务”权限。 此权限使应用能够以 `sub` 声明的形式接收用户的唯一标识符。 它还会向应用提供对 UserInfo 终结点的访问权限。 可以在 Microsoft 标识平台令牌终结点使用 `openid` 范围获取 ID 令牌，应用程序可以使用这些令牌进行身份验证。

### <a name="email"></a>电子邮件

`email` 范围可与 `openid` 范围和任何其他范围一起使用。 它以 `email` 声明的形式向应用提供对用户主要电子邮件地址的访问权限。 仅当电子邮件地址与用户帐户相关联时，才会在令牌中包含 `email` 声明，这种情况并非总是如此。 如果使用 `email` 范围，则应用应准备好处理 `email` 声明不存在于令牌中的情况。

### <a name="profile"></a>个人资料

`profile` 范围可与 `openid` 范围和任何其他范围一起使用。 它使应用可以访问大量关于用户的信息。 它可以访问的信息包括但不限于用户的名字、姓氏、首选用户名和对象 ID。 有关指定用户的 id_token 参数中可用配置文件声明的完整列表，请参阅 [`id_tokens` 参考](id-tokens.md)。

### <a name="offline_access"></a>offline_access

[`offline_access` 范围](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)允许应用在较长时间内代表用户访问资源。 在同意页上，此范围将显示为“维持对已授予访问权限的数据的访问”权限。 当用户批准 `offline_access` 范围时，你的应用程序可以接收来自 Microsoft 标识平台令牌终结点的刷新令牌。 刷新令牌的生存期较长。 旧的访问令牌过期时，应用可获取新的访问令牌。

> [!NOTE]
> 此权限始终显示在所有同意屏幕上，甚至适用于未提供刷新令牌（[隐式流](v2-oauth2-implicit-grant-flow.md)）的流。  这是为了涵盖客户端可以在隐式流中开始，然后移到需要刷新令牌的代码流的情况。

在 Microsoft 标识平台（对 v2.0 终结点发出的请求）上，你的应用程序必须显式请求 `offline_access` 范围才能接收刷新令牌。 这意味着，当在 [OAuth 2.0 授权代码流](active-directory-v2-protocols.md)中兑换 authorization_code 时，只从 `/token` 终结点接收 access_token。 访问令牌在短期内有效。 访问令牌的有效期通常为一小时。 到时，应用需要将用户重定向回到 `/authorize` 终结点以获取新的 authorization_code。 在此重定向期间，根据应用的类型，用户或许无需再次输入其凭据或重新同意权限。 

有关如何获取和使用刷新令牌的详细信息，请参阅[Microsoft 标识平台协议参考](active-directory-v2-protocols.md)。

## <a name="requesting-individual-user-consent"></a>请求单个用户的同意

在 [OpenID Connect 或 OAuth 2.0](active-directory-v2-protocols.md) 授权请求中，应用可以使用 `scope` 查询参数来请求它所需的权限。 例如，当用户登录应用程序时，应用发送如下示例所示的请求（包含换行符以便于阅读）：

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 参数是应用程序所请求的委托权限列表（以空格分隔）。 将权限值附加到资源的标识符（应用程序 ID URI）可指示权限。 在请求示例中，应用需要相应的权限来读取用户的邮箱和以用户身份发送邮件。

用户输入其凭据后，Microsoft 标识平台终结点将检查*用户同意*的匹配记录。 如果用户过去没有同意任何请求的权限，也没有代表整个组织同意这些权限的管理员，则 Microsoft 标识平台终结点会要求用户授予所请求的权限。

> [!NOTE]
> 在此期间，`offline_access`（“维持对已授予访问权限的数据的访问”）和 `user.read`（“登录并读取配置文件”）权限将自动包含在对应用程序的初始许可中。  这些权限通常是应用功能正常所必需 - `offline_access` 授予应用对刷新令牌（对本机和 Web 应用十分重要）的访问权限，而 `user.read` 授予对 `sub` 声明的访问权限，允许客户端或应用随时间推移正确标识用户并访问基本用户信息。  

![显示工作帐户同意的示例屏幕截图](./media/v2-permissions-and-consent/work_account_consent.png)

当用户批准权限请求时，会记录许可，使用户在后续登录到应用程序时无需再次许可。

## <a name="requesting-consent-for-an-entire-tenant"></a>请求整个租户的同意

组织购买应用程序的许可证或订阅时，需要主动设置组织所有成员使用的应用程序。 在此过程中，管理员可以代表租户中的任何用户授予对该应用程序的许可。 如果管理员为整个租户授予许可，则组织的用户不会看到该应用程序的许可页。

若要为租户中的所有用户请求许可委托的权限，应用可以使用管理员许可终结点。

此外，应用程序必须使用管理员许可终结点来请求应用程序权限。

## <a name="admin-restricted-permissions"></a>管理员限制的权限

Microsoft 生态系统中的某些高特权权限可以设置为受管理员限制。 此类权限的示例包括：

* 使用 `User.Read.All` 读取所有用户的完整个人资料
* 使用 `Directory.ReadWrite.All` 将数据写入组织的目录
* 使用 `Groups.Read.All` 读取组织目录中的所有组

虽然使用者用户可以授予应用程序对此类数据的访问权限，但组织用户会受到限制，无法授予对同一敏感公司数据集的访问权限。 如果你的应用程序向组织用户请求对其中一个权限的访问权限，则该用户将收到一条错误消息，指出他们无权同意你的应用程序的权限。

如果应用需要访问组织的这些受管理员限制的范围，同样应该使用管理员同意终结点直接向公司管理员请求相关权限，如下所述。

如果应用程序请求高特权的委托权限，而管理员通过管理员许可终结点授予这些权限，则为租户中的所有用户授予许可。

如果应用程序正在请求应用程序权限，并且管理员通过管理员同意终结点授予这些权限，则不会代表任何特定用户完成此授予。 而是直接为客户端应用程序授予权限。 这些类型的权限仅由守护程序服务和在后台运行的其他非交互式应用程序使用。

## <a name="using-the-admin-consent-endpoint"></a>使用管理员同意终结点

> [!NOTE] 
> 请注意，在使用管理员许可终结点授予管理员许可后，你已完成授予管理员许可，并且用户无需执行任何其他操作。 授予管理员许可后，用户可以通过典型的身份验证流获取访问令牌，并且生成的访问令牌将具有许可的权限。 

当公司管理员使用你的应用程序并定向到授权终结点时，Microsoft 标识平台将检测用户的角色，并询问他们是否要代表整个租户许可请求的权限。 但是，如果你想要主动请求管理员代表整个租户授予权限，则还可以使用一个专用的管理员许可终结点。 对于请求应用程序权限（无法使用授权终结点请求），也需要使用此终结点。

如果你遵循了这些步骤，则应用就能为租户中的所有用户请求权限，包括受管理员限制的范围。 这是一个高特权操作，只能根据方案的需要来执行。

若要查看实现步骤的代码示例，请参阅[受管理员限制的范围示例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>在应用注册门户中请求权限

管理员许可不接受范围参数，因此，必须在应用程序的注册中以静态方式定义所请求的任何权限。 通常，最佳做法是确保静态为给定应用程序定义的权限是它将以动态/增量方式请求的权限的超集。

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>若要为应用程序配置静态请求的权限列表

1. 在 Azure 门户中，[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中转到你的应用程序，或[创建应用](quickstart-register-app.md)（如果尚未这样做）。
2. 找到 " **Api 权限**" 部分，并在 API 权限中单击 "添加权限"。
3. 从可用 Api 列表中选择 " **Microsoft Graph** "，并添加应用所需的权限。
3. **保存**应用注册。

### <a name="recommended-sign-the-user-into-your-app"></a>建议：让用户登录到你的应用

生成使用管理员同意终结点的应用程序时，应用需要一个页面/视图，使管理员能够批准应用的权限。 此页面可以是应用注册流的一部分、应用设置的一部分，或专用“连接”流的一部分。 在许多情况下，合理的结果是应用只在用户使用工作或学校 Microsoft 帐户登录之后才显示此“连接”视图。

将用户登录到应用后，便可识别管理员所属的组织，然后要求他们批准必要的权限。 尽管在严格意义上不需要这样做，但有助于为组织用户带来更直观的体验。 若要将用户登录，请遵循[Microsoft 标识平台协议教程](active-directory-v2-protocols.md)。

### <a name="request-the-permissions-from-a-directory-admin"></a>向目录管理员请求权限

如果已准备好向组织管理员请求权限，可以将用户重定向到 Microsoft 标识平台*管理员许可终结点*。

```
// Line breaks are for legibility only.
  GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
  &scope=
  https://graph.microsoft.com/calendars.read 
  https://graph.microsoft.com/mail.send
```


| 参数     | 条件     | Description                                                                               |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | 需要 | 要向其请求权限的目录租户。 可以采用 GUID 或友好名称格式提供或使用 `common` 以一般方式引用，如示例所示。 |
| `client_id` | 需要 | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `redirect_uri` | 需要 |要向其发送响应以供应用处理的重定向 URI。 其必须与在门户中注册的重定向 URI 之一完全匹配。 |
| `state` | 推荐 | 同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 在发出身份验证请求出现之前，使用该状态对有关用户在应用中的状态的信息（例如前面所在的页面或视图）进行编码。 |
|`scope`        | 需要      | 定义应用程序请求的权限集。 这可以是静态的（使用/.default）或动态作用域。  这可能包括 OIDC 范围（`openid`、`profile``email`）。 | 


此时，Azure AD 会要求租户管理员进行登录来完成请求。 系统会要求管理员批准在 `scope` 参数中请求的所有权限。  如果使用了静态（`/.default`）值，则它的作用类似于1.0 版管理员同意终结点，并请求为在应用所需的权限中找到的所有范围请求许可。

#### <a name="successful-response"></a>成功的响应

如果管理员批准了应用的权限，成功响应如下所示：

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 参数 | Description |
| --- | --- |
| `tenant` | 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。 |
| `state` | 同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 该状态用于对发出身份验证请求出现之前，有关用户在应用中的状态的信息（例如前面所在的页面或视图）编码。 |
| `admin_consent` | 将设置为 `True`。 |

#### <a name="error-response"></a>错误响应

如果管理员未批准应用的权限，失败响应如下所示：

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 参数 | Description |
| --- | --- |
| `error` | 用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别错误根本原因的具体错误消息。 |

从管理员同意终结点收到成功响应后，应用便已获得所请求的权限。 接下来，可以请求所需资源的令牌。

## <a name="using-permissions"></a>使用权限

在用户同意应用程序的权限之后，应用程序即可获取访问令牌，而这些令牌表示应用程序访问资源的权限。 访问令牌只能用于单个资源，但其内部编码是应用已授予该资源的所有权限。 若要获取访问令牌，你的应用可以向 Microsoft 标识平台令牌终结点发出请求，如下所示：

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

可以在对资源的 HTTP 请求中使用生成的访问令牌。 它会向资源可靠地指示应用具有执行特定任务的适当权限。 

有关 OAuth 2.0 协议以及如何获取访问令牌的详细信息，请参阅[Microsoft 标识平台终结点协议参考](active-directory-v2-protocols.md)。

## <a name="the-default-scope"></a>/.default 范围

可以使用 `/.default` 范围帮助将应用从 v2.0 终结点迁移到 Microsoft 标识平台终结点。 这是每个引用应用程序注册时配置的权限静态列表的应用程序的内置范围。 值为 `scope` 的 `https://graph.microsoft.com/.default` 从功能上与 v1.0 终结点 `resource=https://graph.microsoft.com` 相同 - 也就是说，它请求具有 Microsoft Graph 上的范围的令牌，应用程序在 Azure 门户中已注册 Microsoft Graph。

/.Default 范围可用于任何 OAuth 2.0 流，[但在代理流和](v2-oauth2-on-behalf-of-flow.md)[客户端凭据流](v2-oauth2-client-creds-grant-flow.md)中是必需的。  

> [!NOTE]
> 客户端不能在单个请求中合并静态（`/.default`）和动态许可。 因此，`scope=https://graph.microsoft.com/.default+mail.read` 将因范围类型组合而导致错误。

### <a name="default-and-consent"></a>/.default 和 consent

`/.default` 范围也可触发 `prompt=consent` 的 v1.0 终结点行为。 它将请求应用程序所注册的所有权限的许可，而不考虑相关资源。 如果包含为请求的一部分，则 `/.default` 范围将返回一个令牌，其中包含所请求资源的作用域。

### <a name="default-when-the-user-has-already-given-consent"></a>用户已授予许可时为 /.default

由于 `/.default` 功能上等同于 `resource`-centric v1.0 终结点的行为，因此也附带了 v1.0 终结点的许可行为。 也就是说，如果用户在客户端和资源之间未授予任何权限，则 `/.default` 仅触发许可提示。 如果存在任何此类许可，则将返回一个令牌，该令牌包含由该资源的用户授予的所有范围。 但是，如果尚未授予任何权限，或未提供 `prompt=consent` 参数，则将对客户端应用程序注册的所有范围显示许可提示。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>示例1：用户或租户管理员已授予权限

用户（或租户管理员）已授予客户端 Microsoft Graph 权限 `mail.read` 和 `user.read`。 如果客户端发出 `scope=https://graph.microsoft.com/.default` 请求，则不会显示任何许可提示，而不考虑针对 Microsoft Graph 的客户端应用程序注册权限的许可。 将返回包含范围 `mail.read` 和 `user.read` 的令牌。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>示例2：用户未授予客户端和资源之间的权限

客户端和 Microsoft Graph 之间不存在任何用户许可。 客户端已针对 `user.read` 和 `contacts.read` 权限，以及 Azure Key Vault 范围`https://vault.azure.net/user_impersonation`注册。 当客户端请求用于 `scope=https://graph.microsoft.com/.default` 的令牌时，用户将看到用于 `user.read`、`contacts.read`和 Key Vault `user_impersonation` 范围的许可屏幕。 返回的令牌中将仅包含 `user.read` 和 `contacts.read` 范围。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>示例3：用户已同意，并且客户端请求其他范围

用户已针对客户端同意 `mail.read`。 客户端已在其注册中注册 `contacts.read` 范围。 当客户端使用 `scope=https://graph.microsoft.com/.default` 发出令牌请求，并通过 `prompt=consent` 请求许可时，用户将看到一个许可屏幕，该屏幕仅显示由应用程序注册的所有权限。 许可屏幕中将显示 `contacts.read`，但不会显示 `mail.read`。 返回的令牌将用于 Microsoft Graph，并且将包含 `mail.read` 和 `contacts.read`。

### <a name="using-the-default-scope-with-the-client"></a>对客户端使用 /.default 范围

`/.default` 范围的一种特殊情况是客户端请求其自己的 `/.default` 范围。 以下示例演示了这种情况。

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

这将产生显示所有已注册权限（如果根据许可和 `/.default` 的上述说明适用）的许可屏幕，然后返回 id_token，而不是访问令牌。  对于从 ADAL 迁移到 MSAL 的某些旧客户端，此行为存在，不应由面向 Microsoft 标识平台终结点的新客户端使用。  

## <a name="troubleshooting-permissions-and-consent"></a>权限和许可故障排除

如果你或你的应用程序的用户在同意过程中出现意外错误，请参阅此文，了解故障排除步骤：[对应用程序执行许可时出现意外错误](../manage-apps/application-sign-in-unexpected-user-consent-error.md)。
