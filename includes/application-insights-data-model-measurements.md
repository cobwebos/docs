---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172708"
---
自定义度量值的集合。 使用此集合报告与遥测项关联的命名度量值。 典型用例如下：
- 依赖项遥测有效负载的大小
- 由请求遥测处理的队列项数
- 客户完成向导步骤完成事件遥测中的步骤所花的时间。

可以在 Application Analytics 中查询[自定义度量值](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)：

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > 与所属遥测项关联的自定义度量值。 这些度量值可以通过遥测项（包含这些度量值）采样。 使用[指标遥测](../articles/azure-monitor/app/api-custom-events-metrics.md)跟踪其值独立于其他遥测类型的度量值。

最大密钥长度：150
