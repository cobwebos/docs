---
title: 在 Azure Functions 中使用代理
description: 有关如何使用 Azure Functions 代理的概述
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 5e756258bb92d7def195959d909068e87e765c0f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562060"
---
# <a name="work-with-azure-functions-proxies"></a>使用 Azure Functions 代理

本文介绍了如何配置和使用 Azure Functions 代理。 使用此功能可在 Function App 上指定由其他资源实现的终结点。 可以使用这些代理将大型 API 拆分到多个 Function App 中（与在微服务体系结构中一样），同时对客户端仍然呈现为单个 API 接口。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> 标准版 Functions 针对代理执行收费。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。

## <a name="create-a-proxy"></a><a name="create"></a>创建代理

本部分介绍如何在 Functions 门户中创建代理。

1. 打开 [Azure 门户]，并转到 Function App。
2. 在左窗格中，选择“新建代理”。 
3. 为代理提供一个名称。
4. 通过指定**路由模板**和 **HTTP 方法**配置在此 Function App 上公开的终结点。 这些参数的行为取决于 [HTTP 触发器]的规则。
5. 将“后端 URL”  设置为另一个终结点。 此终结点可以是其他 Function App 中的函数，也可以是任何其他 API。 该值不需要是静态值，并且可以引用[应用程序设置]和[原始客户端请求中的参数]。
6. 单击**创建**。

代理现在已作为新终结点存在于 Function App 上。 从客户端角度来看，它等同于 Azure Functions 中的 HttpTrigger。 可以通过复制代理 URL 并使用最喜欢的 HTTP 客户端对其进行测试来试验新代理。

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>修改请求和响应

使用 Azure Functions 代理可以修改针对后端发出的请求以及从后端返回的响应。 这些转换可以使用[使用变量]中定义的变量。

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>修改后端请求

默认情况下，后端请求初始化为原始请求的副本。 除了设置后端 URL 以外，还可以对 HTTP 方法、标头和查询字符串参数进行更改。 修改的值可以引用[应用程序设置]和[原始客户端请求中的参数]。

可在门户中通过展开代理详细信息页的“请求替代”  部分修改后端请求。 

### <a name="modify-the-response"></a><a name="modify-response"></a>修改响应

默认情况下，客户端响应初始化为后端响应的副本。 可对响应的状态代码、原因短语、标头和正文进行更改。 修改的值可以引用[应用程序设置]、[原始客户端请求中的参数]和[后端响应中的参数]。

可在门户中通过展开代理详细信息页的“响应替代”  部分修改后端请求。 

## <a name="use-variables"></a><a name="using-variables"></a>使用变量

代理的配置不需要是静态的。 可将它调整为使用原始客户端请求、后端响应或应用程序设置中的变量。

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>引用本地函数
可以使用 `localhost` 直接引用同一函数应用内的函数，而无需往返代理请求。

`"backendurl": "https://localhost/api/httptriggerC#1"` 将引用路由 `/api/httptriggerC#1` 中的本地 HTTP 触发函数

 
>[!Note]  
>如果函数使用“函数”、“管理员”或“sys”  授权级别，将需要根据原始函数 URL 提供代码和 clientId。 在这种情况下，引用将如下所示：`"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` 我们建议将这些密钥存储在[应用程序设置]中，并在代理中引用这些密钥。 这样可以避免在源代码中存储机密。 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>引用请求参数

可将请求参数用作后端 URL 属性的输入，或者在修改请求和响应的过程中使用请求参数。 一些参数可以从基本代理配置中指定的路由模板绑定，而其他一些参数可以来自传入请求的属性。

#### <a name="route-template-parameters"></a>路由模板参数
可按名称引用路由模板中使用的参数。 需将参数名称括在大括号 ({}) 中。

例如，如果代理具有诸如 `/pets/{petId}` 的路由模板，则后端 URL 可以包括 `{petId}` 的值，就像在 `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` 中那样。 如果路由模板以通配符结尾，例如 `/api/{*restOfPath}`，则值 `{restOfPath}` 将是传入请求中的其余路径段的字符串表示形式。

#### <a name="additional-request-parameters"></a>其他请求参数
除了路由模板参数以外，还可以在配置值中使用以下值：

* **{request.method}** ：对原始请求使用的 HTTP 方法。
* **{request.headers.\<HeaderName\>}** ：从原始请求中读取的标头。 请将 *\<HeaderName\>* 替换为要读取的标头的名称。 如果该标头未包含在请求中，则该值为空字符串。
* **{request.querystring.\<ParameterName\>}** ：可从原始请求中读取的查询字符串参数。 请将 *\<ParameterName\>* 替换为要读取的参数的名称。 如果该参数未包含在请求中，则该值为空字符串。

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>引用后端响应参数

在修改返回给客户端的响应过程中，可以使用响应参数。 可以在配置值中使用以下值：

* **{backend.response.statusCode}** ：在后端响应中返回的 HTTP 状态代码。
* **{backend.response.statusReason}** ：在后端响应中返回的 HTTP 原因短语。
* **{backend.response.headers.\<HeaderName\>}** ：可以从后端响应中读取的标头。 请将 *\<HeaderName\>* 替换为要读取的标头的名称。 如果该标头未包含在响应中，则该值将为空字符串。

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>引用应用程序设置

还可以通过将设置名称括在百分号 (%) 之间来引用[针对 Function App 定义的应用程序设置](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)。

例如，后端 URL *https://%ORDER_PROCESSING_HOST%/api/orders* 会将“%ORDER_PROCESSING_HOST%”替换为 ORDER_PROCESSING_HOST 设置的值。

> [!TIP] 
> 当有多个部署或测试环境时，请为后端主机使用应用程序设置。 这样可以确保始终与该环境的正确后端进行通信。

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>对代理进行故障排除

通过将标志 `"debug":true` 添加到 `proxies.json` 中的任何代理，将启用调试日志记录。 日志存储在 `D:\home\LogFiles\Application\Proxies\DetailedTrace` 中，可通过高级工具 (kudu) 访问。 任何 HTTP 响应也将包含 `Proxy-Trace-Location` 标头，其中包含用于访问日志文件的 URL。

可以通过添加设置为 `true` 的 `Proxy-Trace-Enabled` 标头来从客户端调试代理。 这还会将跟踪结果记录到文件系统，并以响应中标头的形式返回跟踪 URL。

### <a name="block-proxy-traces"></a>阻止代理跟踪

出于安全原因，你可能不想允许任何人调用服务生成跟踪。 没有登录凭据这些人将无法访问跟踪内容，但生成跟踪会占用资源并公开你正在使用函数代理。

通过将 `"debug":false` 添加到 `proxies.json` 中的任何特定代理可完全禁用跟踪。

## <a name="advanced-configuration"></a>高级配置

配置的代理存储在一个 proxies.json 文件中，此文件位于函数应用目录的根目录中  。 使用 Functions 支持的任意[部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)时，可以手动编辑此文件并将其部署为应用的一部分。 

> [!TIP] 
> 如果尚未设置一种部署方法，也可以在门户中使用 proxies.json 文件  。 转到到 Function App，选择“平台功能”，并选择“应用服务编辑器”。   这样，便可以看到 Function App 的整个文件结构并进行更改。

Proxies.json 是由一个代理对象定义的，包括已命名的代理及其定义  。 （可选）可以引用用于代码完成的 [JSON 架构](http://json.schemastore.org/proxies)（如果编辑器支持这样做）。 示例文件可能如下例所示：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

每个代理都有一个友好名称，例如上例中的 *proxy1*。 对应的代理定义对象是由以下属性定义的：

* **matchCondition**：必需 - 一个对象，用于定义触发此代理执行的请求。 它包含两个与 [HTTP 触发器]共享的属性：
    * _methods_：代理响应的 HTTP 方法的数组。 如果未指定此属性，代理将响应路由上的所有 HTTP 方法。
    * _route_：必需 - 定义路由模板，控制代理将响应哪些请求 URL。 与在 HTTP 触发器中不同，此处没有默认值。
* **backendUri**：应当通过代理将请求发送到的后端资源的 URL。 此值可以引用应用程序设置和原始客户端请求中的参数。 如果未包括此属性，则 Azure Functions 以 HTTP 200 OK 进行响应。
* **requestOverrides**：定义对后端请求执行的转换的对象。 请参阅[定义 requestOverrides 对象]。
* **responseOverrides**：定义对客户端响应执行的转换的对象。 请参阅[定义 responseOverrides 对象]。

> [!NOTE] 
> Azure Functions 代理中的 route 属性不接受 Function App 主机配置的 routePrefix 属性   。 如果希望包括一个如 `/api` 等前缀，必须将其包括在 route 属性中  。

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> 禁用单个代理

可以通过将 `"disabled": true` 添加到 `proxies.json` 文件中的代理来禁用单个代理。 这将导致满足 matchCondition 的任何请求返回 404。
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> 应用程序设置

代理行为可以通过多个应用程序设置进行控制。 [Functions App 设置参考](./functions-app-settings.md)中概述了所有这些设置

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> 保留字符（字符串格式设置）

代理使用“\”作为转义符从 JSON 文件中读取所有字符串。 代理还会解释大括号。 请参阅下面的完整示例集。

|字符|转义字符|示例|
|-|-|-|
|{ 或 }|{{ 或 }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>定义 requestOverrides 对象

requestOverrides 对象定义调用后端资源时对请求所做的更改。 该对象由以下属性定义：

* **backend.request.method**：用于调用后端的 HTTP 方法。
* **backend.request.querystring.\<ParameterName\>** ：可为后端调用设置的查询字符串参数。 请将 *\<ParameterName\>* 替换为要设置的参数的名称。 请注意，如果提供了空字符串，则该参数仍包含在后端请求中。
* **backend.request.headers.\<HeaderName\>** ：可为后端调用设置的标头。 请将 *\<HeaderName\>* 替换为要设置的标头的名称。 请注意，如果提供了空字符串，则该参数仍包含在后端请求中。

值可以引用应用程序设置和原始客户端请求中的参数。

示例配置可能如下所示：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>定义 responseOverrides 对象

requestOverrides 对象定义对传回客户端的响应所做的更改。 该对象由以下属性定义：

* **response.statusCode**：要返回给客户端的 HTTP 状态代码。
* **response.statusReason**：要返回给客户端的 HTTP 原因短语。
* **response.body**：要返回给客户端的正文的字符串表示形式。
* **response.headers.\<HeaderName\>** ：可为返回给客户端的响应设置的标头。 请将 *\<HeaderName\>* 替换为要设置的标头的名称。 如果提供空字符串，该标头不会包含在响应中。

值可以引用应用程序设置、原始客户端请求中的参数和后端响应中的参数。

示例配置可能如下所示：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> 在此示例中，响应正文是直接设置的，因此不需要 `backendUri` 属性。 此示例演示如何使用 Azure Functions 代理来模拟 API。

[Azure 门户]: https://portal.azure.com
[HTTP 触发器]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[定义 requestOverrides 对象]: #requestOverrides
[定义 responseOverrides 对象]: #responseOverrides
[应用程序设置]: #use-appsettings
[使用变量]: #using-variables
[原始客户端请求中的参数]: #request-parameters
[后端响应中的参数]: #response-parameters
