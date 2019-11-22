---
title: Azure Active Directory Identity Protection 的 Microsoft Graph API
description: 了解如何从 Azure Active Directory 查询 Microsoft Graph 风险检测和关联信息
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15b9bae1bd901325efdefeaa4db53df2d6b42b44
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275891"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory 标识保护和 Microsoft Graph 入门

Microsoft Graph 是 Microsoft 的统一 API 终结点，并且是 [Azure Active Directory 标识保护](../active-directory-identityprotection.md) API 的主页。 有四个 Api 公开了有关风险用户和登录的信息。使用第一个 API **riskDetection**，可以查询 Microsoft Graph 以获取用户和登录链接的风险检测的列表以及有关检测的相关信息。 使用第二个 API **riskyUsers** 可以在 Microsoft Graph 中查询有关“标识保护”作为风险检测到的用户的信息。 第三个 API **signIn** 可以通过与风险状态、详细信息和级别相关的特定属性在 Microsoft Graph 中查询有关 Azure AD 登录的信息。 使用第四个 API **identityRiskEvents**可以查询 Microsoft Graph 以获取[风险检测](../reports-monitoring/concept-risk-events.md)和相关信息的列表。 IdentityRiskEvents API 将于2020年1月10日弃用;建议改为使用**riskDetections** API。 本文可帮助你开始连接到 Microsoft Graph 并查询这些 API。 如需深入介绍、完整文档以及访问 Graph 浏览器，请参阅 [Microsoft Graph 站点](https://graph.microsoft.io/)或以下 API 的特定参考文档：

* [riskDetection API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [IDENTITYRISKEVENTS API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) *将在2020年1月10日弃用*

## <a name="connect-to-microsoft-graph"></a>连接到 Microsoft Graph

通过 Microsoft Graph 访问“标识保护”数据需要执行四个步骤：

1. 检索域名。
2. 创建新的应用注册。 
3. 使用此密钥和其他一些信息片段在 Microsoft Graph 中进行身份验证，并在其中接收身份验证令牌。 
4. 使用此令牌可对 API 终结点发出请求，并取回“标识保护”数据。

准备工作：

* 需有管理员权限才能在 Azure AD 中创建应用程序
* 租户域的名称（例如 contoso.onmicrosoft.com）。

## <a name="retrieve-your-domain-name"></a>检索域名 

1. 以管理员身份[登录](https://portal.azure.com)到 Azure 门户。 
1. 在左侧导航窗格中，单击“Active Directory”。 

   ![创建应用程序](./media/howto-identity-protection-graph-api/41.png)

1. 在“管理”部分，单击“属性”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/42.png)

1. 复制域名。

## <a name="create-a-new-app-registration"></a>创建新的应用注册

1. 在“Active Directory”页的“管理”部分，单击“应用注册”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/42.png)

1. 在顶部菜单中，单击“新建应用程序注册”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/43.png)

1. 在“创建”页中，执行以下步骤：

   ![创建应用程序](./media/howto-identity-protection-graph-api/44.png)

   1. 在 "**名称**" 文本框中，键入应用程序的名称（例如： Azure AD 风险检测 API 应用程序）。

   1. 在“类型”中，选择“Web 应用程序和/或 Web API”。

   1. 在“登录 URL”文本框中，键入 `http://localhost`。

   1. 单击“创建”。
1. 若要打开“设置”页，在应用程序列表中，单击最新创建的应用注册。 
1. 复制“应用程序 ID”。

## <a name="grant-your-application-permission-to-use-the-api"></a>授予应用程序使用 API 的权限

1. 在“设置”页中，单击“所需权限”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/15.png)

1. 在“所需权限”页顶部的工具栏中，单击“添加”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/16.png)

1. 在“添加 API 访问权限”页中，单击“选择 API”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/17.png)

1. 在“选择 API”页中，选择“Microsoft Graph”，然后单击“选择”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/18.png)

1. 在“添加 API 访问权限”页中，单击“选择权限”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/19.png)

1. 在“启用访问权限”页中，单击“读取所有标识风险信息”，再单击“选择”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/20.png)

1. 在“添加 API 访问权限”页中，单击“完成”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/21.png)

1. 在“所需的权限”页上，单击“授予权限”，再单击“是”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>获取访问密钥

1. 在“设置”页上，单击“密钥”。

   ![创建应用程序](./media/howto-identity-protection-graph-api/23.png)

1. 在“密钥”页中执行以下步骤：

   ![创建应用程序](./media/howto-identity-protection-graph-api/24.png)

   1. 在 "**密钥说明**" 文本框中，键入说明（例如， *Azure AD 风险检测*"）。
   1. 对于“持续时间”，请选择“1 年内”。
   1. 单击“保存”。
   1. 复制此密钥值，然后将其粘贴到安全位置。   
   
   > [!NOTE]
   > 如果丢失此密钥，必须返回到此部分并创建新密钥。 将此密钥保密：持有该密钥的任何人都可以访问数据。
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>在 Microsoft Graph 中进行身份验证并查询标识风险检测 API

现在应已获得：

- 租户域的名称
- 客户端 ID 
- 密钥 

若要进行身份验证，请向 `https://login.microsoft.com` 发送一个 post 请求，并在请求正文中包含以下参数：

- grant_type：“**client_credentials**”
- 资源：`https://graph.microsoft.com`
- client_id：\<你的客户端 ID\>
- client_secret：\<你的密钥\>

如果成功，此命令将返回身份验证令牌。  
若要调用 API，请创建包含以下参数的标头：

```
`Authorization`="<token_type> <access_token>"
```

身份验证时，可以在返回的令牌中找到令牌类型和访问令牌。

将此标头作为请求发送到以下 API URL：`https://graph.microsoft.com/beta/identityRiskEvents`

如果成功，响应中会包含标识风险检测的集合，以及采用 OData JSON 格式的相关数据，在适当的情况下可以分析和处理这些数据。

下面是使用 PowerShell 进行身份验证和调用 API 的示例代码。  
只需添加客户端 ID、密钥和租户域即可。

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>查询 API

这三个 API 提供了大量机会来检索你的组织中有风险的用户和登录的信息。 下面是这些 API 的一些常见用例以及相关的示例请求。 可以使用上面的示例代码或使用 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)运行这些查询。

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>获取所有脱机风险检测 (riskDetection API)

使用 Identity Protection 登录风险策略，可以在实时检测到风险时应用条件。 但对于脱机发现的检测怎么办呢？ 若要了解脱机发生了哪些检测，从而不会触发登录风险策略，可以查询 riskDetection API。

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>获取成功通过了由风险登录策略触发的 MFA 质询的所有用户 (riskyUsers API)

若要了解基于身份保护风险的策略对组织的影响，可以查询成功通过了风险登录策略触发的 MFA 质询的所有用户。 该信息可以帮助你了解“标识保护”可能错误地将哪些用户检测为存在风险，以及哪些合法用户可能正在执行人工智能认为有风险的操作。

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>获取特定用户的所有有风险的登录 (signIn API)

当你认为某个用户可能已受到威胁时，可以通过检索其所有有风险的登录来更好地了解其风险状态。 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>后续步骤

恭喜，已向 Microsoft Graph 发出了第一个调用！  
现在，可以在适当的情况下查询标识风险检测和使用数据。

若要详细 Microsoft Graph 以及如何使用图形 API 构建应用程序，请查看[文档](https://docs.microsoft.com/graph/overview)。同时，[Microsoft Graph 站点](https://developer.microsoft.com/graph)上提供了更丰富的信息。 

如需相关信息，请参阅：

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md)
- [Azure Active Directory Identity Protection 检测到的风险检测类型](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Microsoft Graph 概述](https://developer.microsoft.com/graph/docs)
- [Azure AD 标识保护服务根](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
