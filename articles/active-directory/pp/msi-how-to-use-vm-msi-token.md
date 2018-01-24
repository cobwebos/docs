---
title: "在 VM 上，如何使用用户分配的托管服务标识来获取访问令牌。"
description: "有关使用 Azure VM 的用户分配的 MSI 获取 OAuth 访问令牌的分步说明和示例。"
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c9bf052ecb2e9c79e0eb627a0fd709d587125cd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2017
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>为 VM 用户分配的托管服务标识 (MSI) 获取访问令牌

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] 本文提供有关获取令牌的各种代码和脚本示例，以及有关处理令牌过期和 HTTP 错误等重要主题的指导。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

如果打算使用本文中的 Azure PowerShell 示例，请务必安装最新版本的 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)。

> [!IMPORTANT]
> - 本文中的所有示例代码/脚本假设客户端在已启用 MSI 的虚拟机上运行。 在 Azure 门户中使用 VM 的“连接”功能远程连接到 VM。 有关在 VM 上启用 MSI 的详细信息，请参阅[使用 Azure CLI 为 VM 配置用户分配的托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)，或参阅在不同的工具中（使用门户、PowerShell、模板或 Azure SDK）执行此操作的文章之一。 

## <a name="overview"></a>概述

客户端应用程序可以请求 MSI [仅限应用的访问令牌](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token)用于访问给定的资源。 该令牌[基于 MSI 服务主体](msi-overview.md#how-does-it-work)。 因此，客户端无需注册自身即可使用自己的服务主体获取访问令牌。 该令牌适合在[需要客户端凭据的服务到服务调用](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md)中用作持有者令牌。

|  |  |
| -------------- | -------------------- |
| [使用 HTTP 获取令牌](#get-a-token-using-http) | MSI 令牌终结点的协议详细信息 |
| [使用 CURL 获取令牌](#get-a-token-using-curl) | 从 Bash/CURL 客户端使用 MSI REST 终结点的示例 |
| [处理令牌过期](#handling-token-expiration) | 有关处理过期访问令牌的指导 |
| [错误处理](#error-handling) | 有关处理从 MSI 令牌终结点返回的 HTTP 错误的指导 |
| [Azure 服务的资源 ID](#resource-ids-for-azure-services) | 在何处获取受支持 Azure 服务的资源 ID |

## <a name="get-a-token-using-http"></a>使用 HTTP 获取令牌 

用于获取访问令牌的基本接口基于 REST，因此，在 VM 上运行的、可发出 HTTP REST 调用的任何客户端应用程序都可以访问该接口。 此接口类似于 Azure AD 编程模型，不过，客户端需使用虚拟机上的 localhost 终结点（而不是使用 Azure AD 终结点）。

示例请求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1
Metadata: true
```

| 元素 | 说明 |
| ------- | ----------- |
| `GET` | HTTP 谓词，指示想要从终结点检索数据。 在本例中，该数据为 OAuth 访问令牌。 | 
| `http://localhost:50342/oauth2/token` | MSI 终结点，其中，50342 是可配置的默认端口。 |
| `resource` | 一个查询字符串参数，表示目标资源的应用 ID URI。 它也会显示在所颁发令牌的 `aud`（受众）声明中。 本示例请求一个用于访问 Azure 资源管理器的、应用 ID URI 为 https://management.azure.com/ 的令牌。 |
| `client_id` | 查询字符串参数，指示了代表用户分配的 MSI 的服务主体的客户端 ID（也称为应用 ID）。 创建用户分配的 MSI 期间，将在 `clientId` 属性中返回此值。 此示例请求客户端 ID 为“712eac09-e943-418c-9be6-9fd5c91078bl”的令牌。 |
| `Metadata` | 一个 HTTP 请求标头字段，MSI 需要使用该元素来缓解服务器端请求伪造 (SSRF) 攻击。 必须将此值设置为“true”（全小写）。

示例响应：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| 元素 | 说明 |
| ------- | ----------- |
| `access_token` | 请求的访问令牌。 调用受保护 REST API 时，该令牌将作为“持有者”令牌嵌入在 `Authorization` 请求标头字段中，使 API 能够对调用方进行身份验证。 | 
| `expires_in` | 访问令牌在过期之前保持有效的秒数，从颁发时间开始算起。 可在令牌的 `iat` 声明中找到颁发时间。 |
| `expires_on` | 访问令牌过期的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `exp` 声明）。 |
| `not_before` | 访问令牌生效且可被接受的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `nbf` 声明）。 |
| `resource` | 请求访问令牌时所针对的资源，与请求的 `resource` 查询字符串参数匹配。 |
| `token_type` | 令牌的类型，这是一个“持有者”访问令牌，意味着资源可向此令牌的持有者授予访问权限。 |
| `client_id` | 表示用户分配的 MSI 的服务主体的客户端 ID（也称为应用 ID），为该 MSI 请求令牌。 |

## <a name="get-a-token-using-curl"></a>使用 CURL 获取令牌

请确保用用户分配的 MSI 的服务主体的客户端 ID（也称为应用 ID）替换 `client_id` 参数的 <MSI CLIENT ID> 值。 创建用户分配的 MSI 期间，将在 `clientId` 属性中返回此值。

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   示例响应：

   ```bash
   user@vmLinux:~$ response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl" -H Metadata:true -s)
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>处理令牌过期

本地 MSI 子系统会缓存令牌。 因此，可以根据所需的频率调用令牌，仅当存在以下情况时，才在线调用 Azure AD 结果：
- 由于缓存中没有令牌，发生了缓存未命中
- 令牌已过期

如果将令牌缓存在代码中，应该准备好处理资源指出令牌已过期的情况。

## <a name="error-handling"></a>错误处理。 

MSI 终结点通过 HTTP 响应消息标头的状态代码字段，以 4xx 或 5xx 错误的形式指示错误：

| 状态代码 | 错误原因 | 处理方式 |
| ----------- | ------------ | ------------- |
| 请求中出现 4xx 错误。 | 一个或多个请求参数不正确。 | 请勿重试。  查看错误详细信息了解更多信息。  4xx 错误属于设计时错误。|
| 服务出现 5xx 暂时性错误。 | MSI 子系统或 Azure Active Directory 返回了暂时性错误。 | 至少等待 1 秒后可以安全重试。  如果重试过快或过于频繁，Azure AD 可能返回速率限制错误 (429)。|

如果发生错误，相应的 HTTP 响应正文将包含 JSON 和错误详细信息：

| 元素 | 说明 |
| ------- | ----------- |
| error   | 错误标识符。 |
| error_description | 错误的详细说明。 **错误说明随时可能更改。请不要编写会根据错误说明中的值生成分支片段的代码。**|

### <a name="http-response-reference"></a>HTTP 响应参考

本部分介绍可能的错误响应。 “200 OK”状态表示成功的响应，访问令牌包含在响应正文 JSON 的 access_token 元素中。

| 状态代码 | 错误 | 错误说明 | 解决方案 |
| ----------- | ----- | ----------------- | -------- |
| 400 错误请求 | invalid_resource | AADSTS50001：在名为 *\<TENANT-ID\>* 的租户中找不到名为 *\<URI\>* 的应用程序。 如果应用程序尚未由租户管理员安装，或者尚未获得租户中的任何用户同意，则可能会发生这种情况。 可能将身份验证请求发送给了错误的租户。\ | （仅限 Linux） |
| 400 错误请求 | bad_request_102 | 未指定必需的元数据标头 | 请求中缺少 `Metadata` 请求标头字段，或者该字段的格式不正确。 必须将该值指定为 `true`（全小写）。 有关示例，请参阅[使用 HTTP 获取令牌](#get-a-token-using-http)中的“示例请求”。|
| 401 未授权 | unknown_source | 未知源 *\<URI\>* | 检查是否已正确设置 HTTP GET 请求 URI 的格式。 必须将 `scheme:host/resource-path` 部分指定为 `http://localhost:50342/oauth2/token`。 有关示例，请参阅[使用 HTTP 获取令牌](#get-a-token-using-http)中的“示例请求”。|
|           | invalid_request | 请求中缺少必需的参数、包含无效的参数值、多次包含某个参数，或格式不正确。 |  |
|           | unauthorized_client | 客户端无权使用此方法请求访问令牌。 | 此错误是由某个未使用本地环回调用扩展的请求，或者在未正确配置 MSI 的 VM 上发出请求造成的。 如需 VM 配置方面的帮助，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)。 |
|           | access_denied | 资源所有者或授权服务器拒绝了请求。 |  |
|           | unsupported_response_type | 授权服务器不支持使用此方法获取访问令牌。 |  |
|           | invalid_scope | 请求的范围无效、未知或格式不正确。 |  |
| 500 内部服务器错误 | 未知 | 无法从 Active Directory 检索令牌。 有关详细信息，请参阅 \<文件路径\> 中的日志 | 检查是否已在 VM 上启用 MSI。 如需 VM 配置方面的帮助，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)。<br><br>另请检查是否已正确设置 HTTP GET 请求 URI 的格式，尤其是查询字符串中指定的资源 URI。 有关示例，请参阅[使用 HTTP 获取令牌](#get-a-token-using-http)中的“示例请求”；有关服务的列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID

有关支持 Azure AD 且已使用 MSI 进行测试的资源列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](msi-overview.md#azure-services-that-support-azure-ad-authentication)。


## <a name="next-steps"></a>后续步骤

- 若要在 Azure VM 上启用 MSI，请参阅[使用 Azure CLI 为 VM 配置用户分配的托管服务标识 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用以下评论部分提供反馈，帮助我们改进内容。








