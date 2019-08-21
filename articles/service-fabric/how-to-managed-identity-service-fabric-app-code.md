---
title: Azure Service Fabric-将托管标识用于 Service Fabric 应用程序 |Microsoft Docs
description: 如何使用 Service Fabric 应用程序代码中的托管标识
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 7/25/2019
ms.author: atsenthi
ms.openlocfilehash: f0944e9fddc0afb28f758ba7b16232330d3bc34d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635529"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services-preview"></a>如何利用 Service Fabric 应用程序的托管标识访问 Azure 服务 (预览版)

Service Fabric 应用程序可以利用托管标识访问支持基于 Azure Active Directory 身份验证的其他 Azure 资源。 应用程序可以获取代表其标识的[访问令牌](../active-directory/develop/developer-glossary.md#access-token), 该令牌可能是系统分配的, 也可能是用户分配的, 并使用它作为 "持有者" 令牌向其他服务 (也称为[受保护的资源服务器](../active-directory/develop/developer-glossary.md#resource-server)) 进行身份验证。 令牌表示分配给 Service Fabric 应用程序的标识, 只会颁发给共享该标识的 Azure 资源 (包括 SF 应用程序)。 有关托管标识的详细说明以及系统分配的标识和用户分配的标识之间的区别, 请参阅[托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)文档。 在本文中, 我们将以[客户端应用程序](../active-directory/develop/developer-glossary.md#client-application)的形式引用启用了托管身份的 Service Fabric 应用程序。

> [!IMPORTANT]
> 托管标识表示与包含资源的订阅关联的相应 Azure AD 租户中的 Azure 资源和服务主体之间的关联。 因此, 在 Service Fabric 环境中, 仅在部署为 Azure 资源的应用程序中支持托管标识。 

> [!IMPORTANT]
> 在使用 Service Fabric 应用程序的托管标识之前, 必须向客户端应用程序授予对受保护资源的访问权限。 请参阅[支持 Azure AD 身份验证的 Azure 服务](/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources)列表, 以查看支持, 然后访问各自服务的文档, 以了解向访问相关资源授予标识访问权限的特定步骤。 

## <a name="acquiring-an-access-token-using-rest-api"></a>使用 REST API 获取访问令牌
在为托管标识启用的群集中, Service Fabric 运行时公开一个 localhost 终结点, 应用程序可以使用该终结点获取访问令牌。 此终结点在群集的每个节点上都可用, 且可供该节点上的所有实体访问。 授权的调用方可以通过调用此终结点并提供身份验证代码来获取访问令牌;此代码由每个不同的服务代码包激活的 Service Fabric 运行时生成, 并绑定到托管该服务代码包的进程的生存期。

具体而言, 启用了托管身份的 Service Fabric 服务的环境将使用以下变量进行种子设定:
- "MSI_ENDPOINT": 本地主机终结点、完整路径、API 版本和与该服务的托管标识对应的参数
- "MSI_SECRET": 身份验证代码, 它是一个不透明的字符串, 用于唯一表示当前节点上的服务

> [!NOTE]
> 名称 "MSI_ENDPOINT" 和 "MSI_SECRET" 是指以前指定的托管标识 ("托管服务标识"), 现已弃用。 这些名称还与其他支持托管标识的 Azure 服务使用的等效环境变量名称一致。

> [!IMPORTANT]
> 应用程序代码应将 "MSI_SECRET" 环境变量的值视为敏感数据, 而不应记录它或传送。 身份验证代码没有本地节点以外的值, 或者在托管服务的进程终止后, 它确实表示 Service Fabric 服务的标识, 因此应使用与访问令牌本身相同的预防措施来处理。

若要获取令牌, 客户端将执行以下步骤:
- 通过将托管标识终结点 (MSI_ENDPOINT 值) 与令牌所需的 API 版本和资源 (受众) 串联在一起形成 URI
- 为指定的 URI 创建 GET http 请求
- 将身份验证代码 (MSI_SECRET 值) 添加为请求的标头
- 提交请求

成功的响应将包含表示生成的访问令牌的 JSON 有效负载以及描述该令牌的元数据。 失败的响应还将包括对失败的说明。 有关错误处理的更多详细信息, 请参阅下文。

将 Service Fabric 在不同级别 (节点、群集、资源提供程序服务) 对访问令牌进行缓存, 因此成功的响应不一定意味着令牌是直接发出的, 以响应用户应用程序的请求。 将缓存标记, 使其不超过其生存期, 因此, 应用程序可以确保接收有效的令牌。 建议应用程序代码将其自身所获得的任何访问令牌缓存起来;缓存键应包括受众的 (派生)。 


示例请求：
```http
GET 'http://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://keyvault.azure.com/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
其中：

| 元素 | 描述 |
| ------- | ----------- |
| `GET` | HTTP 谓词，指示想要从终结点检索数据。 在本例中，该数据为 OAuth 访问令牌。 | 
| `http://localhost:2377/metadata/identity/oauth2/token` | Service Fabric 应用程序的托管标识终结点, 通过 MSI_ENDPOINT 环境变量提供。 |
| `api-version` | 查询字符串参数, 指定托管标识令牌服务的 API 版本;目前唯一接受的值为`2019-07-01-preview`, 并且可能会发生更改。 |
| `resource` | 一个查询字符串参数，表示目标资源的应用 ID URI。 这将反映为`aud`已颁发令牌的 (受众) 声明。 此示例请求一个令牌访问 Azure Key Vault, 其应用 ID URI 为 https://keyvault.azure.com/ 。 |
| `Secret` | HTTP 请求标头字段, 该字段是 Service Fabric 托管标识令牌服务为 Service Fabric 服务的身份验证调用方进行身份验证所必需的。 此值由 SF 运行时通过 MSI_SECRET 环境变量提供。 |


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
| `token_type` | 标记的类型;在这种情况下, 为 "持有者" 访问令牌, 这意味着此令牌的表示形式 ("持有者") 是令牌的预期使用者。 |
| `access_token` | 请求的访问令牌。 调用受保护 REST API 时，该令牌将作为“持有者”令牌嵌入在 `Authorization` 请求标头字段中，使 API 能够对调用方进行身份验证。 | 
| `expires_on` | 访问令牌到期的时间戳;表示为从 "1970-01-01T0: 0: 0Z UTC" 开始的秒数, 并对应于令牌的`exp`声明。 在这种情况下, 该令牌将在 2019-08-08-08T06:10:11 + 00:00 (RFC 3339 中) 过期|
| `resource` | 为其颁发访问令牌的资源, 通过请求的`resource`查询字符串参数指定; 对应于令牌的 "aud" 声明。 |


## <a name="acquiring-an-access-token-using-c"></a>使用获取访问令牌C#
以上内容成为, 在C#中:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;

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

                var accessToken = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                return accessToken.Trim('"');
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
此示例基于上述示例, 演示如何使用托管标识访问存储在 Key Vault 中的机密。

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

            Console.WriteLine($"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Console.WriteLine("\n== Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    var secretMetadata = secretResponse.Body.ToString();
                    Console.WriteLine($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    Console.WriteLine($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
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

            Console.WriteLine(response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority"></param>
        /// <param name="resource"></param>
        /// <param name="scope"></param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            if (config.DoVerboseLogging)
                Console.WriteLine($"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";
            if (config.DoVerboseLogging)
                Console.WriteLine($"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            if (config.DoVerboseLogging)
                Console.WriteLine($"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            if (config.DoVerboseLogging)
                Console.WriteLine($"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var accessToken = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            if (config.DoVerboseLogging)
                Console.WriteLine("returning access code..");

            return accessToken.Trim('"');
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
```

## <a name="error-handling"></a>错误处理
HTTP 响应标头的 "状态代码" 字段指示请求的成功状态;"200 正常" 状态表示成功, 响应将包括如上所述的访问令牌。 下面是可能的错误响应的简短枚举。

| 状态代码 | 错误原因 | 处理方式 |
| ----------- | ------------ | ------------- |
| 404 未找到。 | 未知的身份验证代码, 或未向应用程序分配托管标识。 | 纠正应用程序设置或令牌获取代码。 |
| 429 请求过多 |  达到限制时, 由 AAD 或 SF 施加限制。 | 使用指数补偿重试。 请参阅下面的指南。 |
| 请求中出现 4xx 错误。 | 一个或多个请求参数不正确。 | 请勿重试。  查看错误详细信息了解更多信息。  4xx 错误属于设计时错误。|
| 服务出现5xx 错误。 | 托管标识子系统或 Azure Active Directory 返回了暂时性错误。 | 请在一小段时间后重试。 重试时, 可能会遇到阻止条件 (429)。|

如果发生错误, 相应的 HTTP 响应正文将包含包含错误详细信息的 JSON 对象:

| 元素 | 描述 |
| ------- | ----------- |
| code | 错误代码。 |
| correlationId | 可用于调试的相关 ID。 |
| 消息 | 错误的详细说明。 **错误说明随时可能更改。不要依赖于错误消息本身。**|

示例错误:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

下面列出了特定于托管标识的典型 Service Fabric 错误:

| 代码 | 消息 | 描述 | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | 在请求标头中找不到密钥。 | 请求未提供身份验证代码。 | 
| ManagedIdentityNotFound | 找不到指定应用程序主机的托管标识。 | 应用程序没有标识, 或者身份验证代码未知。 |
| ArgumentNullOrEmpty | 参数 "resource" 不应为 null 或空字符串。 | 请求中未提供资源 (受众)。 |
| InvalidApiVersion | 不支持 api 版本 ""。 支持的版本为 "2019-07-01-preview"。 | 请求 URI 中指定的 API 版本丢失或不受支持。 |
| InternalServerError | 出现了错误。 | 托管标识子系统中出现错误, 可能在 Service Fabric 堆栈外。 最可能的原因是为资源指定的值不正确 (检查尾部的 "/"？) | 

## <a name="retry-guidance"></a>重试指南 

通常, 唯一可重试错误代码为 429 (请求过多);内部服务器错误/5xx 错误代码可能是可重试的, 尽管原因可能是永久性的。 

限制限制适用于对托管标识子系统所进行的调用次数 (特别是 "上游" 依赖项 (托管标识 Azure 服务或安全令牌服务)。 Service Fabric 在管道中的不同级别缓存令牌, 但对于所涉及组件的分布式性质, 调用方可能会遇到不一致的限制响应 (即, 在应用程序的一个节点/实例上受到限制, 而不是在请求同一标识的令牌时使用不同的节点。)设置限制条件后, 来自同一应用程序的后续请求可能会失败, 并出现 HTTP 状态代码 429 (请求过多), 直到清除该条件。  

建议由于使用指数回退重试限制而失败的请求, 如下所示: 

| 调用索引 | 收到429时的操作 | 
| --- | --- | 
| 1 | 等待1秒, 然后重试 |
| 2 | 等待2秒, 然后重试 |
| 3 | 等待4秒, 然后重试 |
| 4 | 等待8秒, 然后重试 |
| 4 | 等待8秒, 然后重试 |
| 5 | 等待16秒, 然后重试 |

## <a name="resource-ids-for-azure-services"></a>Azure 服务的资源 ID
有关支持 Azure AD 的资源列表及其各自的资源 Id, 请参阅[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-msi.md)。

## <a name="next-steps"></a>后续步骤
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [向 Azure Service Fabric 应用程序授予其他 Azure 资源的访问权限](./how-to-grant-access-other-resources.md)