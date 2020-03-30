---
title: Azure 函数 HTTP 输出绑定
description: 了解如何在 Azure 函数中返回 HTTP 响应。
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277617"
---
# <a name="azure-functions-http-output-bindings"></a>Azure 函数 HTTP 输出绑定

通过 HTTP 输出绑定响应 HTTP 请求发送者。 此绑定需要使用 HTTP 触发器，利用此绑定，可以自定义与触发器请求相关联的响应。

HTTP 触发函数的默认返回值为：

- `HTTP 204 No Content`函数 2.x 及更高版本中的空实体
- `HTTP 200 OK`函数 1.x 中空实体

## <a name="configuration"></a>Configuration

下表解释了在 function.json** 文件中设置的绑定配置属性。 在 C# 类库中，没有与这些 function.json** 属性对应的属性。

|properties  |描述  |
|---------|---------|
| **type** |必须设置为 `http`。 |
| direction**** | 必须设置为 `out`。 |
| **name** | 在响应的函数代码中使用的变量名称，或者 `$return` 以使用返回值。 |

## <a name="usage"></a>使用情况

若要发送 HTTP 响应，请使用语言标准响应模式。 在 C# 或 C# 脚本中，使函数返回类型为 `IActionResult` 或 `Task<IActionResult>`。 在 C# 中，不需要返回值属性。

有关示例响应，请参阅[触发器示例](./functions-bindings-http-webhook-trigger.md#example)。

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 及更高版本中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的 2.x 版及更高版本设置。 若要详细了解 2.x 版及更高版本中的全局配置设置，请参阅 [Azure Functions 的 host.json 参考](functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md#http)。

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|properties  |默认 | 描述 |
|---------|---------|---------| 
| customHeaders|none|使你可以在 HTTP 响应中设置自定义标头。 前面的示例将 `X-Content-Type-Options` 标头添加到响应中，以避免内容类型探查。 |
|dynamicThrottlesEnabled|真<sup>\*</sup>|启用此选项后，此设置会导致请求处理管道定期检查系统性能计数器，例如`connections/threads/processes/memory/cpu/etc`，如果这些计数器中的任何一个超过内置高阈值 （80%），则请求将被拒绝，然后`429 "Too Busy"`响应，直到计数器返回到正常水平。<br/><sup>\*</sup>消耗计划中的默认值为`true`。 专用计划中的默认值为`false`。|
|hsts|未启用|`isEnabled` 设置为 `true` 时，将强制执行 [.NET Core 的 HTTP 严格传输安全性 (HSTS) 行为](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts)，如 [`HstsOptions` 类](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)中所定义。 上面的示例还将[`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge)属性设置为 10 天。 `hsts` 支持的属性包括： <table><tr><th>properties</th><th>描述</th></tr><tr><td>excludedHosts</td><td>未添加 HSTS 标头的主机名的字符串数组。</td></tr><tr><td>includeSubDomains</td><td>布尔值，指示是否启用了 Strict-Transport-Security 标头的 includeSubDomain 参数。</td></tr><tr><td>maxAge</td><td>定义 Strict-Transport-Security 标头的 max-age 参数的字符串。</td></tr><tr><td>preload</td><td>布尔值，指示是否启用了 Strict-Transport-Security 标头的 preload 参数。</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|并行执行的 HTTP 函数数目上限。 此值允许您控制并发性，这有助于管理资源利用率。 例如，您可能具有一个使用大量系统资源（内存/cpu/套接字）的 HTTP 函数，以便当并发性过高时，它会导致问题。 或者，您可能具有向第三方服务发出出站请求的功能，并且这些调用需要限制速率。 在这种情况下，应用限制可能有帮助。 <br/><sup>*</sup>消耗计划的默认值为 100。 专用计划的默认值为无界 。`-1`|
|maxOutstandingRequests|200<sup>\*</sup>|在任意给定时间搁置的未完成请求数上限。 此限制包括已排队但尚未开始执行的请求，以及正在执行的所有请求。 超出此限制的任何传入请求将被拒绝，并返回 429“太忙”响应。 允许调用方使用基于时间的重试策略，还可帮助控制最大请求延迟。 此设置仅控制脚本宿主执行路径中发生的排队。 其他队列（例如 ASP.NET 请求队列）仍有效，不受此设置的影响。 <br/><sup>\*</sup>消耗计划的默认值为 200。 专用计划的默认值为无界 。`-1`|
|routePrefix|api|应用到所有路由的路由前缀。 使用空字符串可删除默认前缀。 |

## <a name="next-steps"></a>后续步骤

- [从 HTTP 请求运行函数](./functions-bindings-http-webhook-trigger.md)