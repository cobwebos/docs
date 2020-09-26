---
title: 保护 Azure Functions
description: 了解如何使 Azure 中运行的函数代码更安全，使其免遭常见攻击的威胁。
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: e48991788307a47d0e01a7921e0c94d77ddcd5ad
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294744"
---
# <a name="securing-azure-functions"></a>保护 Azure Functions

对于 Web 或云托管应用程序来说，无服务器函数的安全开发、部署和操作的规划在诸多方面都几乎相同。 [Azure 应用服务](../app-service/index.yml)提供函数应用的托管基础结构。 本文介绍了运行函数代码的安全策略，以及应用服务如何帮助你保护函数。 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

如需查看遵循 [Azure 安全基准检验](../security/benchmarks/overview.md)的一组安全建议，请参阅 [Azure Functions 的 Azure 安全基线](security-baseline.md)。

## <a name="secure-operation"></a>安全操作 

本部分将指导你尽可能安全地配置和运行函数应用。 

### <a name="security-center"></a>安全中心

安全中心与门户中的函数应用集成。 它免费提供了对潜在配置相关安全漏洞的快速评估。 在专用计划中运行的函数应用也可以使用安全中心的实时安全功能，但需要额外付费。 若要了解更多信息，请参阅[保护你的 Azure 应用服务 Web 应用和 API](../security-center/security-center-app-services.md)。 

### <a name="log-and-monitor"></a>日志和监视器

检测攻击的方法之一是通过活动监视活动和日志分析。 Functions 与 Application Insights 相集成，可收集函数应用的日志、性能和错误数据。 Application Insights 可自动检测性能异常，并且包含了强大的分析工具来帮助你诊断问题并了解函数的使用方式。 若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

Functions 还与 Azure Monitor 日志集成，使你能够将函数应用日志与系统事件合并，以便更轻松地进行分析。 你可以使用诊断设置将函数的平台日志和指标流式导出配置到你选择的目标位置，例如 Log Analytics 工作区。 若要了解更多信息，请参阅[使用 Azure Monitor 日志监视 Azure Functions](functions-monitor-log-analytics.md)。 

对于企业级威胁检测和响应自动化，请将日志和事件流式传输到 Log Analytics 工作区。 然后，可以将 Azure Sentinel 连接到此工作区。 若要了解详细信息，请参阅[什么是 Azure Sentinel](../sentinel/overview.md)。  

有关可观测性的更多安全建议，请参阅 [Azure Functions 的 Azure 安全基线](security-baseline.md#logging-and-monitoring)。 

### <a name="require-https"></a>需要 HTTPS

默认情况下，客户端可以使用 HTTP 或 HTTPS 连接到函数终结点。 应将 HTTP 重定向到 HTTPS，因为 HTTPS 使用 SSL/TLS 协议来提供安全连接，该连接经过了加密和身份验证。 若要了解如何操作，请参阅[实施 HTTPS](../app-service/configure-ssl-bindings.md#enforce-https)。

需要 HTTPS 时，还需要最新的 TLS 版本。 若要了解如何操作，请参阅[实施 TLS 版本](../app-service/configure-ssl-bindings.md#enforce-tls-versions)。

有关详细信息，请参阅 [保护连接 (TSL)](../app-service/overview-security.md#https-and-certificates)。

### <a name="function-access-keys"></a>函数访问密钥

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>系统密钥 

特定扩展可能需要系统管理的密钥才能访问 webhook 终结点。 系统密钥适合为内部组件调用的特定于扩展的函数终结点。 例如，[事件网格触发器](functions-bindings-event-grid-trigger.md)要求订阅在调用触发器终结点时使用系统密钥。 Durable Functions 还使用系统密钥调用 [Durable 任务扩展 API](durable/durable-functions-http-api.md)。 

系统密钥的范围由扩展决定，但通常适用于整个函数应用。 系统密钥只能由特定扩展创建，并且不能显式设置其值。 与其他密钥一样，你可从门户或使用密钥 API 为密钥生成新值。

#### <a name="keys-comparison"></a>密钥之间的比较

下表比较了不同类型的访问密钥的用法：

| 操作                                        | 范围                    | 有效密钥         |
|-----------------------------------------------|--------------------------|--------------------|
| 执行函数                            | 特定函数        | 函数           |
| 执行函数                            | 任何函数             | 函数或主机   |
| 调用管理员终结点                        | 函数应用             | 主机（仅限 master） |
| 调用 Durable 任务扩展 API              | 函数应用<sup>1</sup> | 系统<sup>2</sup> |
| 调用特定于扩展的 Webhook（内部） | 函数应用<sup>1</sup> | 系统<sup>2</sup> |

<sup>1</sup>范围由扩展决定。  
<sup>2</sup>按扩展设置的特定名称。

若要了解有关访问密钥的更多信息，请参阅 [HTTP 触发器绑定文章](functions-bindings-http-webhook-trigger.md#obtaining-keys)。


#### <a name="secret-repositories"></a>机密存储库

默认情况下，密钥存储在通过 `AzureWebJobsStorage` 设置提供的帐户中的 Blob 存储容器中。 可以使用特定的应用程序设置来重写此行为，将密钥存储在另一位置。

|位置  |设置 | Value | 描述  |
|---------|---------|---------|---------|
|不同的存储帐户     |  `AzureWebJobsSecretStorageSas`       | `<BLOB_SAS_URL` | 根据提供的 SAS URL，将密钥存储在另一个存储帐户的 Blob 存储中。 在使用函数应用特有的机密存储密钥之前对密钥进行加密。 |
|文件系统   | `AzureWebJobsSecretStorageType`   |  `files`       | 密钥持久保留在文件系统中，在使用函数应用特有的机密进行存储之前加密。 |
|Azure Key Vault  | `AzureWebJobsSecretStorageType`<br/>`AzureWebJobsSecretStorageKeyVaultName` | `keyvault`<br/>`<VAULT_NAME>` | 保管库必须有一项与承载资源的系统分配托管标识相对应的访问策略。 访问策略应向标识授予以下机密权限：`Get`、`Set`、`List` 和 `Delete`。 <br/>在本地运行时使用开发人员标识，且设置必须位于 [local.settings.json 文件](functions-run-local.md#local-settings-file)中。 | 
|Kubernetes 机密  |`AzureWebJobsSecretStorageType`<br/>`AzureWebJobsKubernetesSecretName`（可选） | `kubernetes`<br/>`<SECRETS_RESOURCE>` | 仅当在 Kubernetes 中运行 Functions 运行时时才受支持。 如果未设置 `AzureWebJobsKubernetesSecretName`，则会将存储库视为只读。 在这种情况下，必须在部署之前生成值。 在部署到 Kubernetes 时，Azure Functions Core Tools 会自动生成值。|

### <a name="authenticationauthorization"></a>身份验证/授权

虽然函数密钥可以为不需要的访问提供一些缓解措施，但真正保护函数终结点的唯一方法是实现对访问函数的客户端的主动身份验证。 然后，你可以根据身份做出授权决策。  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>权限

与任何应用程序或服务一样，目标是以尽可能低的权限运行函数应用。 

#### <a name="user-management-permissions"></a>用户管理权限

函数支持内置 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。 函数支持的 Azure 角色有[参与者](../role-based-access-control/built-in-roles.md#contributor)、[所有者](../role-based-access-control/built-in-roles.md#owner)和[读者](../role-based-access-control/built-in-roles.md#owner)。 

权限在函数应用级别有效。 参与者角色是执行大多数函数应用级任务所必需的。 只有所有者角色才能删除函数应用。 

#### <a name="organize-functions-by-privilege"></a>按权限组织函数 

应用程序设置中存储的连接字符串和其他凭据为函数应用中的所有函数提供了关联资源中相同的权限集。 请考虑将具有特定凭据访问权限的函数数量降至最少，具体方法是将不使用这些凭据的函数移动到单独的函数应用中。 你始终可以使用诸如[函数链](/learn/modules/chain-azure-functions-data-using-bindings/)之类的技术在不同函数应用中的函数之间传递数据。  

#### <a name="managed-identities"></a>托管标识

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

有关详细信息，请参阅[如何使用应用服务和 Azure Functions 的托管标识](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)。

#### <a name="restrict-cors-access"></a>限制 CORS 访问

[跨源资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 是一种允许在另一个域中运行的 Web 应用向 HTTP 触发器终结点发出请求的方法。 应用服务为在 HTTP 请求中处理所需的 CORS 标头提供了内置支持。 CORS 规则是在函数应用级别定义的。  

虽然很容易使用通配符来允许所有站点访问终结点。 但这违背了 CORS 的目的，CORS 的目的是帮助防止跨站点脚本攻击。 相反，请为必须访问终结点的每个 Web 应用的域添加单独的 CORS 条目。 

### <a name="managing-secrets"></a>管理机密 

为了能够连接到运行代码所需的各种服务和资源，函数应用需要能够访问机密，如连接字符串和服务密钥。 本节介绍如何存储函数所需的机密。

切勿在函数代码中存储机密。 

#### <a name="application-settings"></a>应用程序设置

默认情况下，请将函数应用使用的连接字符串和机密以及绑定存储为应用程序设置。 这样，函数代码和函数使用的各种绑定就都可以使用这些凭据。 应用程序设置（密钥）名称用于检索实际值，即机密。 

例如，每个函数应用都需要一个关联的存储帐户，运行时将使用该帐户。 默认情况下，与此存储帐户的连接存储在名为 `AzureWebJobsStorage` 的应用程序设置中。

应用设置和连接字符串以加密方式存储在 Azure 中。 只有在应用程序启动时，它们才会被解密，然后再注入到应用程序内存中。 加密密钥会定期轮换。 如果希望管理机密的安全存储，则应该将应用设置改为对 Azure Key Vault 的引用。 

在本地计算机上开发函数时，还可以在文件的 local.settings.js中默认加密设置。 若要了解详细信息，请参阅 `IsEncrypted` [本地设置文件](functions-run-local.md#local-settings-file)中的属性。  

#### <a name="key-vault-references"></a>Key Vault 引用

虽然应用程序设置足以满足大多数功能，但你可能希望跨多个服务共享相同的机密。 在这种情况下，机密的冗余存储会导致更多潜在的漏洞。 一种更安全的方法是使用中央机密存储服务，并使用对该服务的引用而不是对机密本身的引用。      

[Azure Key Vault](../key-vault/general/overview.md) 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。 你可在应用程序设置中使用 Key Vault 引用来代替连接字符串或密钥。 若要了解详细信息，请参阅[使用应用服务和 Azure Functions 的 Key Vault 引用](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json)。

### <a name="set-usage-quotas"></a>设置使用配额

考虑为在消耗计划中运行的函数设置使用配额。 如果对函数应用中函数的总执行量设置每日 GB-sec 限制，当达到该限制时，将停止执行。 这可能有助于缓解恶意代码执行函数。 若要了解如何估算函数的消耗量，请参阅[估算消耗计划成本](functions-consumption-costs.md)。 

### <a name="data-validation"></a>数据验证

函数使用的触发器和绑定不提供任何额外的数据验证。 代码必须验证从触发器或输入绑定接收到的任何数据。 如果上游服务遭到入侵，你不希望未经验证的输入流经函数。 例如，如果函数将 Azure 存储队列中的数据存储在关系数据库中，则必须验证数据并对命令进行参数化处理以避免 SQL 注入攻击。 

不要以为传入函数的数据已经过验证或清理。 最好是验证写入输出绑定的数据是否有效。 

### <a name="handle-errors"></a>处理错误

虽然它看起来很基础，但这对于在函数中编写良好的错误处理来说非常重要。 未处理的错误从主机中涌现出来，然后由运行时处理。 不同绑定处理错误的方式不同。 有关详细信息，请参阅 [Azure Functions 错误处理](functions-bindings-error-pages.md)。

### <a name="disable-remote-debugging"></a>禁用远程调试

请确保禁用远程调试，除非你正在主动调试函数。 可以在门户中函数应用“配置”的“常规设置”选项卡中禁用远程调试 。 

### <a name="restrict-cors-access"></a>限制 CORS 访问

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

请勿在允许的来源列表中使用通配符。 相反，你可以列出想要从中获取请求的特定域。

### <a name="store-data-encrypted"></a>存储加密数据

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>保护部署

Azure Functions 工具集成可以简化将本地函数项目代码发布到 Azure 的过程。 在考虑 Azure Functions 拓扑的安全性时，请务必了解部署的工作方式。   

### <a name="deployment-credentials"></a>部署凭据

部署应用服务需要一组部署凭据。 这些部署凭据用于保护函数应用部署。 部署凭据由应用服务平台管理，并静态加密。 

部署凭据有两种类型：

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

目前，部署凭据不支持 Key Vault。 若要了解如何管理部署凭据，请参阅[为 Azure 应用服务配置部署凭据](../app-service/deploy-configure-credentials.md)。

### <a name="disable-ftp"></a>禁用 FTP

默认情况下，每个函数应用都启用了 FTP 终结点。 使用部署凭据访问 FTP 终结点。 

不建议使用 FTP 来部署函数代码。 FTP 部署是手动的，需要同步触发器。 若要了解详细信息，请参阅 [FTP 部署](functions-deployment-technologies.md#ftp)。 

如果不打算使用 FTP，则应在门户中禁用它。 如果选择使用 FTP，则应[实施 FTPS](../app-service/deploy-ftp.md#enforce-ftps)。

### <a name="secure-the-scm-endpoint"></a>保护 scm 终结点

每个函数应用都有一个对应的 `scm` 服务终结点，供高级工具 (Kudu) 服务用于部署和其他应用服务[站点扩展](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)。 函数应用的 scm 终结点始终是 `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>` 格式的 URL。 如果使用网络隔离保护函数，还必须考虑此终结点。 

具有独立的 scm 端点使你可以控制针对隔离或在虚拟网络中运行的函数应用的部署和其他高级工具功能。 scm 终结点支持基本身份验证（使用部署凭据）和使用 Azure 门户凭据进行单一登录。 若要了解详细信息，请参阅[访问 Kudu 服务](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)。 

### <a name="continuous-security-validation"></a>持续安全性验证

由于开发过程中的每一步都需要考虑安全性，因此在持续部署环境中实现安全性验证也是有意义的。 这有时称为 DevSecOps。 在部署管道中使用 Azure DevOps，可以将验证集成到部署过程中。 有关详细信息，请参阅[了解如何将持续安全性验证添加到 CI/CD 管道](/azure/devops/migrate/security-validation-cicd-pipeline)。  

## <a name="network-security"></a>网络安全

通过限制对函数应用的网络访问，可以控制谁可以访问函数终结点。 函数利用应用服务基础结构，使函数能够访问资源而无需使用可通过 internet 路由的地址，或将 internet 访问限制为函数终结点。 若要了解有关这些网络选项的详细信息，请参阅 [Azure Functions 网络选项](functions-networking-options.md)。

### <a name="set-access-restrictions"></a>设置访问限制

访问限制使你可以定义允许/拒绝规则列表，以控制应用的流量。 规则按优先顺序进行评估。 如果没有定义规则，则应用将接受来自任何地址的流量。 若要了解更多信息，请参阅 [Azure 应用服务访问限制 #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json)。

### <a name="private-site-access"></a>专用站点访问

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>在隔离环境中部署函数应用

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>使用网关服务

利用网关服务（例如 [Azure 应用程序网关](../application-gateway/overview.md)和 [Azure Front Door](../frontdoor/front-door-overview.md)），你可以设置 Web 应用程序防火墙 (WAF)。 WAF 规则用于监视或阻止检测到的攻击，从而为函数提供额外的保护层。 若要设置 WAF，函数应用需要在 ASE 中运行或使用专用终结点（预览版）。 若要了解详细信息，请参阅[使用专用终结点](../app-service/networking/private-endpoint.md)。    

## <a name="next-steps"></a>后续步骤

+ [适用于 Azure Functions 的 Azure 安全基线](security-baseline.md)
+ [Azure Functions 诊断](functions-diagnostics.md)
