---
title: "如何使用 Azure VM 托管服务标识进行登录和获取令牌"
description: "有关使用 Azure VM MSI 服务主体进行登录以及获取访问令牌的逐步说明。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/04/2017
ms.author: bryanla
ms.openlocfilehash: 2f6cf4709c77ca1bb051b7d5c9e7d1d5d125c343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>如何使用 Azure VM 托管服务标识 (MSI) 进行登录和获取令牌 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]在 Azure VM 上启用 MSI 后，可以使用 MSI 进行登录以及请求访问令牌。 本文介绍使用 MSI [服务主体](develop/active-directory-dev-glossary.md#service-principal-object)进行登录，以及获取[仅限应用的访问令牌](develop/active-directory-dev-glossary.md#access-token)来访问其他资源的各种方法，包括：

- 通过 PowerShell 或 Azure CLI 以无提示/无人参与的方式登录
- 使用 .NET、PowerShell、Bash/CURL 和 REST 等方法获取各种客户端的令牌
- 使用 Azure SDK（包括 .NET、Java、Node.js、Python 和 Ruby）登录

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

如果打算使用本文中的 PowerShell 示例，请务必安装 [Azure PowerShell 版本 4.3.1](https://www.powershellgallery.com/packages/AzureRM) 或更高版本。 如果打算使用本文中的 Azure CLI 示例，可以使用三个选项：
- 在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)。
- 通过 Azure CLI 代码块右上角的“试用”按钮，使用嵌入式 Azure Cloud Shell。
- 如果想要在本地命令提示符中使用 CLI，请[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 


> [!IMPORTANT]
> - 本文中的所有示例代码/脚本假设客户端在已启用 MSI 的虚拟机上运行。 有关在 VM 上启用 MSI 的详细信息，请参阅[使用 Azure 门户配置 VM 托管服务标识 (MSI)](msi-qs-configure-portal-windows-vm.md)，或有关在不同的工具中（使用 PowerShell、CLI、模板或 Azure SDK）执行此操作的文章之一。 
> - 为了防止代码/脚本示例中发生授权错误 (403/AuthorizationFailed)，必须为 VM 的标识授予 VM 范围的“读取者”访问权限，以允许在 VM 上执行 Azure 资源管理器操作。 有关详细信息，请参阅[使用 Azure 门户授予托管服务标识 (MSI) 对资源的访问权限](msi-howto-assign-access-portal.md)。
> - 在继续学习后续部分之前，请在 Azure 门户中使用 VM“连接”功能远程连接到已启用 MSI 的 VM。

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>如何使用 MSI 从 PowerShell 或 CLI 登录

以前，以脚本自身的标识运行该脚本就意味着：
- 要将该脚本作为机密/Web 客户端应用程序注册到 Azure AD
- 使用应用程序的客户端 ID/机密凭据登录

如果使用 MSI，则脚本客户端不再需要注册到 Azure AD，也不需要提供凭据。 以下示例演示如何使用 VM 的 MSI 服务主体进行登录。

### <a name="azure-powershell"></a>Azure PowerShell

以下脚本演示如何：

- 获取 Azure VM 的 MSI 访问令牌
- 使用访问令牌以相应的 MSI 服务主体登录到 Azure AD
- 使用 MSI 服务主体发出 Azure 资源管理器调用，以获取服务主体的 ID

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Azure CLI

以下脚本演示如何：

- 以 VM 的 MSI 服务主体登录到 Azure AD
- 使用 MSI 服务主体发出 Azure 资源管理器调用，以获取服务主体的 ID

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>如何从 MSI 获取访问令牌

使用 MSI 时，客户端应用程序/脚本不再需要注册到 Azure AD，也不需要提供其客户端 ID 和机密，即可获取访问令牌。 客户端只需向本地 MSI 终结点请求访问令牌，而无需提供应用程序凭据。 仅限应用的访问令牌是针对 MSI 服务主体颁发的，适合在[需要客户端凭据的服务到服务调用](active-directory-protocols-oauth-service-to-service.md)中用作持有者令牌。

以下示例演示如何使用 VM 的 MSI 来获取令牌。

### <a name="net"></a>.NET

> [!IMPORTANT]
> Azure AD 身份验证库 (ADAL) 未与 MSI 集成。 若要使用 MSI 获取访问令牌，请向 VM 中的本地 MSI 终结点发出请求。 有关详细信息，请参阅 [MSI 常见问题解答和已知问题](msi-known-issues.md#frequently-asked-questions-faqs)。

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

示例请求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 元素 | 说明 |
| ------- | ----------- |
| `GET` | HTTP 谓词，指示想要从终结点检索数据。 在本例中，该数据为 OAuth 访问令牌。 | 
| `http://localhost:50342/oauth2/token` | MSI 终结点，其中，50342 是可配置的默认端口。 |
| `resource` | 一个查询字符串参数，表示目标资源的应用 ID URI。 它也会显示在所颁发令牌的 `aud`（受众）声明中。 在本示例中，我们要请求一个用于访问 Azure 资源管理器的、应用 ID URI 为 https://management.azure.com/ 的令牌。 |
| `Metadata` | 一个 HTTP 请求标头字段，MSI 需要使用该元素来缓解服务器端请求伪造 (SSRF) 攻击。 必须将此值设置为“true”（全小写）。

示例响应：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 元素 | 说明 |
| ------- | ----------- |
| `access_token` | 请求的访问令牌。 调用 REST API 时，该令牌将作为“持有者”令牌嵌入在 `Authorization` 请求标头字段中，使 API 能够对调用方进行身份验证。 | 
| `refresh_token` | MSI 不使用它。 |
| `expires_in` | 访问令牌在过期之前保持有效的秒数，从颁发时间开始算起。 可在令牌的 `iat` 声明中找到颁发时间。 |
| `expires_on` | 访问令牌过期的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `exp` 声明）。 |
| `not_before` | 访问令牌生效且可被接受的时间范围。 该日期表示为自“1970-01-01T0:0:0Z UTC”开始的秒数（对应于令牌的 `nbf` 声明）。 |
| `resource` | 请求访问令牌时所针对的资源，与请求的 `resource` 查询字符串参数匹配。 |
| `token_type` | 令牌的类型，这是一个“持有者”访问令牌，意味着资源可向此令牌的持有者授予访问权限。 |

## <a name="how-to-sign-in-with-azure-sdk-libraries-using-msi"></a>如何使用 MSI 通过 Azure SDK 库登录

Azure 通过一系列 [Azure SDK](https://azure.microsoft.com/downloads) 支持多个编程平台。 其中几个平台已更新为支持使用 MSI 登录，并提供相应的示例来演示用法。 由于添加了其他支持，此列表已更新：

| SDK 中 IsInRole 中的声明 | 示例 |
| --- | ------ | 
| .NET   | [从已启用 MSI 的 VM 管理资源](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [从已启用 MSI 的 VM 管理存储](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [使用托管服务标识管理资源](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [使用 MSI 从 VM 内部轻松进行身份验证](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [从已启用 MSI 的 VM 管理资源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>其他信息

### <a name="token-expiration"></a>令牌过期

本地 MSI 子系统会缓存令牌。 因此，可以根据所需的频率调用令牌，仅当存在以下情况时，才在线调用 Azure AD 结果：
- 由于缓存中没有令牌，发生了缓存未命中
- 令牌已过期

如果将令牌缓存在代码中，应该准备好处理资源指出令牌已过期的情况。

### <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID

有关支持 MSI 的服务的列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="troubleshooting"></a>故障排除

### <a name="sign-in-or-token-acquisition-fails"></a>登录或令牌获取失败

检查是否已正确启用 MSI。 在 [Azure 门户](https://portal.azure.com)中返回到 Azure VM，并执行以下操作：

- 查看“配置”页，并确保“MSI 已启用”配置为“是”。
- 查看“扩展”页，确保已成功部署 MSI 扩展。

如果有任一项不正确，可能都需要在资源上再次重新部署 MSI，或排查部署故障。

### <a name="http-request-error-responses"></a>HTTP 请求错误响应

- bad_request_102: 未指定必需的元数据标头

  请求中缺少 `Metadata` 请求标头字段，或者该字段的格式不正确。 必须将该值指定为 `true`（全小写）。 有关示例，请参阅[前面 REST 部分](#rest)中的“示例请求”。

- 未知: 无法从 Active directory 检索令牌。有关详细信息，请参阅 \<文件路径\> 中的日志

  检查是否已正确设置 HTTP GET 请求 URI 的格式，尤其是查询字符串中指定的资源 URI。 有关示例，请参阅[前面 REST 部分](#rest)中的“示例请求”；有关服务的列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

- unknown_source: 未知的源 \<URI\>

  检查是否已正确设置 HTTP GET 请求 URI 的格式。 必须将 `scheme:host/resource-path` 部分指定为 `http://localhost:50342/oauth2/token`。 有关示例，请参阅[前面 REST 部分](#rest)中的“示例请求”。

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。
- 若要在 Azure VM 上启用 MSI，请参阅[使用 PowerShell 配置 Azure VM 托管服务标识 (MSI)](msi-qs-configure-powershell-windows-vm.md)。

使用以下评论部分提供反馈，帮助我们改进内容。

