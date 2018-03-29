---
title: 使用 Azure 堆栈 API |Microsoft 文档
description: 了解如何从 Azure 将 API 请求发送到 Azure 堆栈中检索身份验证。
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 3523f86791a3bf437cbd21ba4df5afc0cd1955fd
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
<!--  cblackuk and charliejllewellyn -->

# <a name="use-the-azure-stack-api"></a>使用 Azure 堆栈 API

Azure 堆栈 API 可以用于自动执行操作，如联合应用商店项。

在过程要求你针对 Microsoft Azure 登录终结点的客户端身份验证。 终结点返回一个标记。 提供每个请求发送到 Azure 堆栈 API 的标头中的令牌。 Azure 使用 Oauth2.0。

这篇文章提供到 Azure 堆栈使用 curl 实用程序的特定的简单示例。 本主题将指导完成检索令牌以访问 Azure 堆栈 API 的过程。 大多数语言提供了此类刷新该令牌具有可靠的令牌管理和处理任务的 Oauth 2.0 库。

查看 Azure 堆栈 REST API 与泛型 REST 客户端一起使用的整个过程如 curl 可帮助你了解的基础的请求，以及你可能需要的接收的响应负载中。

这篇文章还不了解可用于检索标记例如交互式登录或创建专用应用程序 Id 的所有选项。 有关详细信息，请参阅[Azure REST API 参考](https://docs.microsoft.com/rest/api/)。

## <a name="get-a-token-from-azure"></a>从 Azure 获取令牌

创建请求*正文*使用格式设置内容类型 x-响应客户-窗体-urlencoded 以获取访问令牌。 发布到 Azure 的 REST 身份验证和登录终结点请求。
```
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**租户 ID**是：

- 你的租户域，如 fabrikam.onmicrosoft.com
- 你的租户 ID，如 8eaed023-2b34-4da1-9baa-8bc8c9d6a491
- 默认值为独立于租户的密钥： 常见

### <a name="post-body"></a>Post 正文
```
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

为每个值：

  **grant_type**
  
  你将使用的身份验证方案的类型。 在此示例中，值是：
  ```
  password
  ```

  **resource**

  资源令牌访问。 可以通过查询 Azure 堆栈管理元数据终结点找到的资源。 查看**受众**部分

  Azure 堆栈管理终结点：
  ```
  https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
  ```
 > [!NOTE]  
 > 如果你是管理员尝试访问租户 API，则你必须确保使用租户终结点，例如https://adminmanagement.{region}.{Azure堆栈域} / 元数据/终结点？ 的 api-version = 2015年-01-011
  
  例如，使用 Azure 堆栈开发工具包中：
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

  此值是硬编码为默认值：
  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  替代选项是可用于特定方案：
  
  | Application | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**
  
  Azure 堆栈 AAD 帐户，例如：
  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  Azure 堆栈 AAD 管理员密码。
  
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

获取你的访问令牌时，需要将其作为标头添加到每个 API 请求。 若要执行此操作，你需要创建的标头**授权**值： `Bearer <access token>`。 例如：

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

### <a name="url-structure-and-query-syntax"></a>URL 的结构和查询语法

一般请求 URI，组成: {URI 方案}:// {URI 主机} / {资源路径}？ {query-string}

- **URI 方案**:  
此 URI 指示用来发送请求的协议。 例如 `http` 或 `https`。
- **URI 主机**:  
主机指定的域名或 IP 地址的服务器托管的 REST 服务终结点的位置，如`graph.microsoft.com`或`adminmanagement.local.azurestack.external`。
- **资源路径**:  
路径指定的资源或资源集合，其中可能包括由确定这些资源的选定内容中的服务的多个段。 例如：`beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners`可用于查询应用程序集合中的特定应用程序的所有者的列表。
- **查询字符串**:  
字符串提供附加的简单参数，如 API 版本或资源选择条件。

## <a name="azure-stack-request-uri-construct"></a>Azure 堆栈请求 URI 构造
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

有关使用 Azure rest 样式的终结点的详细信息，请参阅[Azure REST API 参考](https://docs.microsoft.com/rest/api/)。
