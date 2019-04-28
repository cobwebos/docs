---
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737173"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|属性  |默认 | 描述 |
|---------|---------|---------| 
|routePrefix|api|应用到所有路由的路由前缀。 使用空字符串可删除默认前缀。 |
|maxOutstandingRequests|200*|在任意给定时间搁置的未完成请求数上限。 此限制包括已排队但尚未开始执行的请求，以及正在执行的所有请求。 超出此限制的任何传入请求将被拒绝，并返回 429“太忙”响应。 允许调用方使用基于时间的重试策略，还可帮助控制最大请求延迟。 此设置仅控制脚本宿主执行路径中发生的排队。 其他队列（例如 ASP.NET 请求队列）仍有效，不受此设置的影响。 *版本 1.x 的默认值是无限制的。 消耗计划中版本 2.x 的默认值为 200。 专用计划中版本 2.x 的默认值是无限制的。|
|maxConcurrentRequests|100*|要并行执行的 http 函数数目上限。 这样，可以控制并发性，从而帮助管理资源利用率。 例如，某个 http 函数可能使用了大量系统资源（内存/CPU/插槽），从而在并发性过高时导致问题。 或者，某个函数向第三方服务发出出站请求，则可能需要限制这些调用的速率。 在这种情况下，应用限制可能有帮助。 *版本 1.x 的默认值是无限制的。 消耗计划中版本 2.x 的默认值为 100。 专用计划中版本 2.x 的默认值是无限制的。|
|dynamicThrottlesEnabled|true*|启用时，将为此设置将导致请求处理管道，以定期检查系统性能计数器类似连接/线程/进程/内存/CPU 等，并通过内置的高阈值 (80%)，如果有任何这些计数器请求拒绝与 429“太忙”响应，直至恢复到正常水平的计数器。 *版本 1.x 的默认值是 false。 消耗计划中版本 2.x 的默认值为 true。 专用计划中版本 2.x 的默认值为 false。|

<!-- ms.date: 04/26/2019 -->