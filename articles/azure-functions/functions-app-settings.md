---
title: Azure Functions 的应用设置参考
description: 有关 Azure Functions 应用设置或环境变量的参考文档。
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 4de6f093e43bbb8b3e258c3dd2a71f728beb7287
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769534"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions 的应用设置参考

函数应用中的应用设置包含对该函数应用的所有函数产生影响的全局配置选项。 在本地运行时，这些设置将作为本地[环境变量](functions-run-local.md#local-settings-file)进行访问。 本文列出可在函数应用中使用的应用设置。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) 文件和 [local.settings.json](functions-run-local.md#local-settings-file) 文件中提供了其他全局配置选项。

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

使用 Application Insights 时的 Application Insights 检测密钥。 请参阅[监视 Azure Functions](functions-monitoring.md)。

|密钥|示例值|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

在版本2.x 和更高版本的函数运行时中，将基于运行时环境配置应用程序行为。 此值在[初始化期间](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)会被读取。 可以将 `AZURE_FUNCTIONS_ENVIRONMENT` 设置为任何值，但支持[三个值](/dotnet/api/microsoft.aspnetcore.hosting.environmentname)：[开发](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)、[过渡](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)和[生产](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)。 如果未设置 `AZURE_FUNCTIONS_ENVIRONMENT`，则默认情况下，在本地环境中 `Development`，在 Azure 上 `Production`。 应使用此设置，而不是 `ASPNETCORE_ENVIRONMENT` 设置运行时环境。 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

用于存储日志并在门户上的“监视”选项卡中显示这些日志的可选存储帐户连接字符串。 存储帐户必须是支持 Blob、队列和表的通用帐户。 请参阅[存储帐户](functions-infrastructure-as-code.md#storage-account)和[存储帐户要求](functions-create-function-app-portal.md#storage-account-requirements)。

|密钥|示例值|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> 有关性能和体验，建议使用 APPINSIGHTS_INSTRUMENTATIONKEY 和 App Insights 进行监视，而不使用 AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` 表示禁用针对函数应用根 URL 显示的默认登录页。 默认为 `false`。

|密钥|示例值|
|---|------------|
|AzureWebJobsDisableHomepage|true|

如果省略此应用设置或将其设置为 `false`，则会显示类似于以下示例的页来响应 URL `<functionappname>.azurewebsites.net`。

![函数应用登录页](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` 表示在编译 .NET 代码时使用“发布”模式；`false` 表示使用“调试”模式。 默认为 `true`。

|密钥|示例值|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

要启用的 beta 功能的逗号分隔列表。 由这些标志启用的 Beta 功能尚未准备好用于生产，但在发布这些功能之前可针对试验目的启用这些功能。

|密钥|示例值|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

指定用于密钥存储的存储库或提供程序。 目前，支持的存储库包括 blob 存储（“Blob”）和本地文件系统（“Files”）。 默认为在版本 2 中使用 blob，在版本 1 中使用文件系统。

|密钥|示例值|
|---|------------|
|AzureWebJobsSecretStorageType|文件|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 运行时针对除 HTTP 触发的函数以外的其他所有函数使用此存储帐户连接字符串。 存储帐户必须是支持 Blob、队列和表的通用帐户。 请参阅[存储帐户](functions-infrastructure-as-code.md#storage-account)和[存储帐户要求](functions-create-function-app-portal.md#storage-account-requirements)。

|密钥|示例值|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

用于 TypeScript 的编译器的路径。 允许根据需要重写默认值。

|密钥|示例值|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

指示是否启用了 Azure 门户中的编辑。 有效值为“readwrite”和“readonly”。

|密钥|示例值|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

要在此函数应用中使用的 Functions 运行时版本。 波浪符加主要版本号表示使用该主要版本的最新版本（例如“~2”）。 当同一主要版本的新版本可用时，会自动在函数应用中安装新版本。 若要让应用固定使用特定的版本，请使用完整版本号（例如“2.0.12345”）。 默认为“~2”。 `~1` 的值将应用固定到运行时的版本 1.x。

|密钥|示例值|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>函数\_V2\_兼容性\_模式

此设置使函数应用能够在版本2.x 运行时上以版本 2. x 兼容模式运行。 仅当将[function app 从版本2.x 升级到运行时的版本](functions-versions.md#migrating-from-2x-to-3x)1.x 时遇到问题时才使用此设置。 

>[!IMPORTANT]
> 此设置仅作为短期解决方法，当你将应用程序更新为在版本2.x 上正确运行。 只要[支持1.x 运行时](functions-versions.md)，就支持此设置。 如果在不使用此设置的情况下遇到阻止应用在版本2.x 上运行的问题，请[报告你的问题](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)。

要求[函数\_扩展\_版本](functions-app-settings.md#functions_extension_version)设置为 `~3`。

|密钥|示例值|
|---|------------|
|函数\_V2\_兼容性\_模式|true|

## <a name="functions_worker_process_count"></a>函数\_WORKER\_进程\_计数

指定语言工作进程的最大数量，默认值为 `1`。 允许的最大值为 `10`。 函数调用在语言工作进程之间均匀分布。 每隔10秒生成一次 Language 工作进程，直到达到函数\_WORKER\_进程\_计数。 使用多语言工作进程与[缩放](functions-scale.md)不同。 如果你的工作负荷混合使用了大量的 CPU 和 i/o 界限调用，请考虑使用此设置。 此设置适用于所有 non-.NET 语言。

|密钥|示例值|
|---|------------|
|函数\_WORKER\_进程\_计数|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

要在函数应用中加载的语言辅助角色运行时。  这将对应于应用程序中正在使用的语言（例如，“dotnet”）。 对于多种语言的函数，需要将它们发布到多个应用，每个应用都具有相应的辅助角色运行时值。  有效值为 `dotnet` （C#/F#）、`node` （JavaScript/TypeScript）、`java` （Java）、`powershell` （PowerShell）和 `python` （Python）。

|密钥|示例值|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

仅限 & 高级计划。 存储函数应用代码和配置的存储帐户的连接字符串。 请参阅[创建函数应用](functions-infrastructure-as-code.md#create-a-function-app)。

|密钥|示例值|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

仅限 & 高级计划。 函数应用代码和配置的文件路径。 与 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING 结合使用。 默认值是以函数应用名称开头的唯一字符串。 请参阅[创建函数应用](functions-infrastructure-as-code.md#create-a-function-app)。

|密钥|示例值|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

函数应用可以横向扩展到的最大实例数。 默认值为无限制。

> [!NOTE]
> 此设置是预览功能，只有在设置为值 <= 5 时才可靠

|密钥|示例值|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_仅限 Windows。_  
设置在 Windows 上运行函数应用时使用的 node.js 版本。 应使用波形符（~）使运行时使用目标主版本的最新可用版本。 例如，如果设置为 `~10`，将使用最新版本的 node.js 10。 如果主要版本以颚化符为目标，则无需手动更新次版本。 

|密钥|示例值|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

让函数应用从已装载的包文件运行。

|密钥|示例值|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|第|

有效值是解析为部署包文件位置的 URL 或 `1`。 设置为 `1` 时，包必须位于 `d:\home\data\SitePackages` 文件夹中。 使用此设置的 zip 部署时，包将自动上传到此位置。 在预览版中，此设置名为 `WEBSITE_RUN_FROM_ZIP`。 有关详细信息，请参阅[从包文件运行函数](run-functions-from-deployment-package.md)。

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

默认情况下，Functions 代理将使用快捷方式从代理直接将 API 调用发送到同一 Function App 中的函数，而不是创建新的 HTTP 请求。 此设置让你能够禁用该行为。

|密钥|值|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|使用指向本地 Function App 中某个函数的后端 URL 的调用将不再直接发送到该函数，并将改回定向回 Function App 的 HTTP 前端。|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|这是默认值。 使用指向本地 Function App 中某个函数的后端 URL 的调用将直接转发到该函数|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

此设置控制 %2F 在路由参数插入后端 URL 时是否在路由参数中解码为斜杠。 

|密钥|值|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|包含编码斜杠的路由参数会将其解码。 `example.com/api%2ftest` 将成为 `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|此选项为默认行为。 所有路由参数在传递时将保持不变|

### <a name="example"></a>示例

以下是位于 URL myfunction.com 上的函数应用中的示例 proxies.json

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|URL 解码|输入|输出|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>后续步骤

[了解如何更新应用设置](functions-how-to-use-azure-function-app-settings.md#settings)

[查看 host.json 文件中的全局设置](functions-host-json.md)

[查看应用服务应用的其他应用设置](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
