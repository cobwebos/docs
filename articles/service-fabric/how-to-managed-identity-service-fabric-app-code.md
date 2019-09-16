---
title: Azure Service Fabric-将托管标识用于 Service Fabric 应用程序 |Microsoft Docs
description: 如何通过 Service Fabric 应用程序代码使用托管标识
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: 6a3d33954bda0605e752555922914a9fd432d8c1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968219"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>如何利用 Service Fabric 应用程序的托管标识访问 Azure 服务（预览版）

Service Fabric 应用程序可以利用托管标识来访问支持基于 Azure Active Directory 的身份验证的其他 Azure 资源。 应用程序可以获取代表其标识（由系统分配或用户分配）的[访问令牌](../active-directory/develop/developer-glossary.md#access-token)，并使用该令牌作为“持有者”令牌在其他服务（也称为[受保护的资源服务器](../active-directory/develop/developer-glossary.md#resource-server)）中验证自己的身份。 令牌表示分配给 Service Fabric 应用程序的标识，只会颁发给共享该标识的 Azure 资源（包括 SF 应用程序）。 有关托管标识的详细介绍以及系统分配的标识和用户分配的标识之间的区别，请参阅[托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)文档。 在整篇文章中，我们将支持托管标识的 Service Fabric 应用程序称作[客户端应用程序](../active-directory/develop/developer-glossary.md#client-application)。

> [!IMPORTANT]
> 托管标识表示 Azure 资源与相应 Azure AD 租户（该租户与包含该资源的订阅相关联）中的服务主体之间的关联。 因此，在 Service Fabric 的上下文中，只有部署为 Azure 资源的应用程序才支持托管标识。 

> [!IMPORTANT]
> 在使用 Service Fabric 应用程序的托管标识之前，必须为客户端应用程序授予对受保护资源的访问权限。 请参阅[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)列表来了解支持情况，然后参阅相关服务的文档，以获取为标识授予对相关资源的访问权限的具体步骤。 

## <a name="acquiring-an-access-token-using-rest-api"></a>使用 REST API 获取访问令牌
在支持托管标识的群集中，Service Fabric 运行时将公开一个 localhost 终结点，应用程序可使用该终结点获取访问令牌。 该终结点将在群集的每个节点上提供，可供该节点上的所有实体访问。 已获授权的调用方可以通过调用此终结点并提供身份验证代码来获取访问令牌；每次激活不同的服务代码包时，此代码将由 Service Fabric 运行时生成，并且此代码与托管该服务代码包的进程的生存期紧密相关。

具体而言，支持托管标识的 Service Fabric 服务的环境中植入了以下变量：
- “MSI_ENDPOINT”：localhost 终结点，包含对应于该服务的托管标识的路径、API 版本和参数
- “MSI_SECRET”：身份验证代码，它是一个不透明字符串，用于唯一表示当前节点上的服务

> [!NOTE]
> 名称“MSI_ENDPOINT”和“MSI_SECRET”是指以前的托管标识名称（“托管服务标识”），现已弃用。 这些名称也与其他支持托管标识的 Azure 服务使用的等效环境变量名称相一致。

> [!IMPORTANT]
> 应用程序代码应将“MSI_SECRET”环境变量的值视为敏感数据 - 不应记录或散播该值。 身份验证代码没有本地节点外部的值，在托管服务的进程终止后，它也不会保留任何值，但它确实代表 Service Fabric 服务的标识，因此，应该像对待访问令牌本身一样对其采取预防措施。

若要获取令牌，客户端将执行以下步骤：
- 通过将托管标识终结点（MSI_ENDPOINT 值）与令牌所需的 API 版本和资源（受众）串联到一起来构成 URI
- 为指定的 URI 创建 GET HTTP 请求
- 将身份验证代码（MSI_SECRET 值）添加为请求的标头
- 提交请求

成功的响应将包含一个 JSON 有效负载，该有效负载表示生成的访问令牌，以及用于描述该令牌的元数据。 失败的响应还包含有关失败的说明。 有关错误处理的更多详细信息，请参阅下文。

Service Fabric 将在不同的级别（节点、群集、资源提供程序服务）缓存访问令牌，因此，成功的响应不一定表示在用户应用程序请求的响应中直接颁发了令牌。 令牌的缓存期限不超过其生存期，因此，可以保证应用程序收到有效的令牌。 建议让应用程序代码自行缓存它所获得的任何访问令牌；缓存密钥应包括受众（派生对象）。 


示例请求：
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
其中：

| 元素 | 描述 |
| ------- | ----------- |
| `GET` | HTTP 谓词，指示想要从终结点检索数据。 在本例中，该数据为 OAuth 访问令牌。 | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Service Fabric 应用程序的托管标识终结点，通过 MSI_ENDPOINT 环境变量提供。 |
| `api-version` | 一个查询字符串参数，指定托管标识令牌服务的 API 版本；目前唯一接受的值为 `2019-07-01-preview`，将来可能会有更改。 |
| `resource` | 一个查询字符串参数，表示目标资源的应用 ID URI。 此元素以已颁发令牌的 `aud`（受众）声明形式反映。 此示例请求一个令牌访问 Azure Key Vault，其应用 ID URI 为 https：\//keyvault.azure.com/。 |
| `Secret` | 一个 HTTP 请求标头字段，Service Fabric 服务的 Service Fabric 托管标识令牌服务需使用该字段对调用方进行身份验证。 此值由 SF 运行时通过 MSI_SECRET 环境变量提供。 |


示例响应：
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://keyvault.azure.com/"
}
```
其中：

| 元素 | 描述 |
| ------- | ----------- |
| `token_type` | 令牌的类型；在本例中为“持有者”访问令牌，表示此令牌的出示方（“持有者”）是令牌的目标使用者。 |
| `access_token` | 请求的访问令牌。 调用受保护 REST API 时，该令牌将作为“持有者”令牌嵌入在 `Authorization` 请求标头字段中，使 API 能够对调用方进行身份验证。 | 
| `expires_on` | 访问令牌的过期时间戳；以从“1970-01-01T0:0:0Z UTC”开始消逝的秒数表示，对应于令牌的 `exp` 声明。 在本例中，令牌将在 2019-08-08T06:10:11+00:00 过期（符合 RFC 3339）|
| `resource` | 为其颁发访问令牌的资源，通过请求的 `resource` 查询字符串参数指定；对应于令牌的“aud”声明。 |


## <a name="acquiring-an-access-token-using-c"></a>使用 C# 获取访问令牌
在 C# 中，以上代码应编写为：

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("MSI_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("MSI_SECRET");
            var managedIdentityAuthenticationHeader = "secret";
            var managedIdentityApiVersion = "2019-07-01-preview";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);

            try
            {
                var response = await new HttpClient().SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>使用托管标识从 Service Fabric 应用程序访问 Key Vault
此示例基于上述示例，演示如何使用托管标识访问存储在 Key Vault 中的机密。

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>错误处理
HTTP 响应标头的“状态代码”字段指示请求的成功状态；“200 OK”状态表示成功，响应中将包含上述访问令牌。 下面是可能的错误响应的简短枚举。

| 状态代码 | 错误原因 | 处理方式 |
| ----------- | ------------ | ------------- |
| 404 未找到。 | 身份验证代码未知，或者未为应用程序分配托管标识。 | 请纠正应用程序设置或令牌获取代码。 |
| 429 请求过多 |  已达到 AAD 或 SF 施加的限制。 | 使用指数补偿重试。 请参阅下面的指南。 |
| 请求中出现 4xx 错误。 | 一个或多个请求参数不正确。 | 请勿重试。  查看错误详细信息了解更多信息。  4xx 错误属于设计时错误。|
| 服务发生 5xx 错误。 | 托管标识子系统或 Azure Active Directory 返回了暂时性错误。 | 请在片刻时间后重试。 重试时可能会达到限制条件 (429)。|

如果发生错误，相应的 HTTP 响应正文将包含一个 JSON 对象和错误详细信息：

| 元素 | 描述 |
| ------- | ----------- |
| code | 错误代码。 |
| correlationId | 可用于调试的关联 ID。 |
| 消息 | 错误的详细说明。 **错误说明随时可能更改。请不要依赖于错误消息本身。**|

示例错误：
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

下面列出了特定于托管标识的典型 Service Fabric 错误：

| 代码 | 消息 | 描述 | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | 在请求标头中找不到机密。 | 未在请求中提供身份验证代码。 | 
| ManagedIdentityNotFound | 找不到指定应用程序主机的托管标识。 | 应用程序没有标识，或者身份验证代码未知。 |
| ArgumentNullOrEmpty | 参数“resource”不应是 null 或空字符串。 | 未在请求中提供资源（受众）。 |
| InvalidApiVersion | API 版本 '' 不受支持。 支持的版本为“2019-07-01-preview”。 | 请求 URI 中指定的 API 版本缺失或不受支持。 |
| InternalServerError | 出现了错误。 | 托管标识子系统中出现错误，可能超出了 Service Fabric 堆栈的范围。 最有可能的原因是为资源指定的值不正确（请检查尾部的“/”） | 

## <a name="retry-guidance"></a>重试指南 

通常，唯一可重试错误代码是 429（请求过多）；内部服务器错误/5xx 错误代码也许可重试，不过，原因可能是永久性的。 

限制适用于对托管标识子系统 - 具体而言，是“上游”依赖项（托管标识 Azure 服务或安全令牌服务）发出的调用数。 Service Fabric 在管道中的不同级别缓存令牌，但由于所涉及组件的分散性，调用方可能会遇到不一致的限制响应（例如，在请求同一标识的令牌时，在应用程序的一个节点/实例上受到限制，但在其他节点上却不会受到限制。）设置限制条件后，来自同一应用程序的后续请求可能会失败并出现 HTTP 状态代码 429（请求过多），直到清除该条件为止。  

建议使用指数退让来重试由于限制而失败的请求，如下所示： 

| 调用索引 | 收到 429 时的操作 | 
| --- | --- | 
| 1 | 等待 1 秒并重试 |
| 2 | 等待 2 秒并重试 |
| 3 | 等待 4 秒并重试 |
| 4 | 等待 8 秒并重试 |
| 4 | 等待 8 秒并重试 |
| 5 | 等待 16 秒并重试 |

## <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID
有关支持 Azure AD 的资源的列表及其相应资源 ID，请参阅[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-msi.md)。

## <a name="next-steps"></a>后续步骤
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [为 Azure Service Fabric 应用程序授予对其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)