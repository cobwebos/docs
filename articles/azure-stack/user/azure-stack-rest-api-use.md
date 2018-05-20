---
title: 使用 Azure Stack API | Microsoft Docs
description: 了解如何从 Azure 检索身份验证令牌，以向 Azure Stack 发出 API 请求。
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 2bbfe4f829ad5c42a5742fdf08f2d185af627f42
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
<!--  cblackuk and charliejllewellyn -->

# <a name="use-the-azure-stack-api"></a>使用 Azure Stack API

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack API，将合成 Marketplace 项等操作自动化。

使用 API 需要你的客户端对 Microsoft Azure 登录终结点进行身份验证。 终结点会返回用于在每个请求发送到 Azure 堆栈 API 的标头中使用的令牌。 （Microsoft Azure 使用 Oauth 2.0。）

本文提供了使用 curl 实用程序来创建 Azure 堆栈请求的示例。 这些示例演练中检索令牌以访问 Azure 堆栈 API 的过程。 大多数编程语言提供具有可靠令牌的管理和句柄任务此类刷新令牌的 Oauth 2.0 库。

查看通过泛型 REST 客户端使用 Azure 堆栈 REST API，例如 curl 可帮助你了解基础的整个过程请求，并显示你可能需要的接收响应有效负载中。

本文不了解可用于检索标记例如交互式登录或创建专用应用程序 Id 的所有选项。 有关详细信息，请参阅 [Azure REST API 参考](https://docs.microsoft.com/rest/api/)。

## <a name="get-a-token-from-azure"></a>从 Azure 获取令牌

创建请求本文并使用内容类型 x-www-form-urlencoded 设置其格式，以获取访问令牌。 使用 POST 将请求发布到 Azure REST 身份验证和登录终结点。

```
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**租户 ID** 为下列其中一项：

* 你的租户域，如 fabrikam.onmicrosoft.com
* 租户 ID，例如 8eaed023-2b34-4da1-9baa-8bc8c9d6a491
* 租户独立密钥的默认值：common

### <a name="post-body"></a>POST 正文

```
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

对于每个值：

  **grant_type**

  要使用的身份验证方案类型。 在此示例中，值为：

  ```
  password
  ```

  **resource**

  令牌访问的资源。 可以通过查询 Azure Stack 管理元数据终结点找到该资源。 请查看 **audiences** 部分

  Azure Stack 管理终结点：

  ```
  https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
  ```

 > [!NOTE]
 > 如果你是管理员尝试访问租户 API，则你必须确保使用租户终结点，例如:https://adminmanagement.{region}.{Azure堆栈域} / 元数据/终结点？ 的 api-version = 2015年-01-011

  例如，使用 Azure 堆栈开发工具包为终结点：

  ```
  curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
  ```

  响应：

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>示例

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  此值已硬编码为默认值：

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  可供特定方案使用的替代选项：

  
  | Application | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  例如，Azure 堆栈 AAD 帐户：

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  Azure Stack AAD 管理员密码。

### <a name="example"></a>示例

请求：

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

响应：

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API 查询

获取访问令牌后，需将其作为标头添加到每个 API 请求。 为此，需要创建值为 `Bearer <access token>` 的标头**授权**。 例如：

请求：

```
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

响应：

```
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL 结构和查询语法

常规请求 URI，由以下项构成：{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}

- **URI 方案**：  
URI 指示用于发送请求的协议。 例如 `http` 或 `https`。
- **URI 主机**：  
该主机指定 REST 服务终结点所在服务器的域名或 IP 地址，例如 `graph.microsoft.com` 或 `adminmanagement.local.azurestack.external`。
- **资源路径**：  
该路径指定资源或资源集合，其中可能包含服务在确定选择这些资源时所用的多个段。 例如：`beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` 可用于查询应用程序集合中特定应用程序的所有者列表。
- **查询字符串**：  
该字符串提供其他简单参数，例如 API 版本或资源选择条件。

## <a name="azure-stack-request-uri-construct"></a>Azure Stack 请求 URI 构造

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI 语法

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>查询 URI 示例

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>后续步骤

有关使用 Azure RESTful 终结点的详细信息，请参阅 [Azure REST API 参考](https://docs.microsoft.com/rest/api/)。
