---
title: 估计 Azure Functions 的消耗计划成本
description: 了解如何更好地估计在 Azure 中的消耗计划中运行函数应用时可能会产生的成本。
author: ggailey777
ms.author: glenga
ms.date: 9/20/2019
ms.topic: conceptual
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 0ff41eb511ad4513fc9bf5a2ded7ef47b08d12ab
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243306"
---
# <a name="estimating-consumption-plan-costs"></a>估计消耗计划成本

目前有三种类型的适用于在 Azure Functions 中运行的应用的托管计划，每个计划都有自己的定价模型： 

| 计划 | 描述 |
| ---- | ----------- |
| [**料**](functions-scale.md#consumption-plan) | 只需为函数应用运行的时间付费。 此计划包括每个订阅的[免费授予][定价页]。|
| [**价格**](functions-scale.md#premium-plan) | 提供与消耗计划相同的功能和缩放机制，但具有增强的性能和 VNET 访问权限。 成本取决于所选的定价层。 若要了解详细信息，请参阅[Azure Functions 高级计划](functions-premium-plan.md)。 |
| [**专用（应用服务）** ](functions-scale.md#app-service-plan) <br/>（基本层或更高级别） | 需要在专用 Vm 或隔离中运行时，请使用自定义映像，或者想要使用过量的应用服务计划容量。 使用[常规应用服务计划计费](https://azure.microsoft.com/pricing/details/app-service/)。 成本取决于所选的定价层。|

你选择了最能实现函数性能和成本要求的计划。 若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md)。

本文仅处理消耗计划，因为此计划会产生可变成本。 

Durable Functions 还可以在消耗计划中运行。 若要了解有关使用 Durable Functions 时的成本注意事项的详细信息，请参阅[Durable Functions 计费](./durable/durable-functions-billing.md)。

## <a name="consumption-plan-costs"></a>消耗计划成本

单个函数执行的执行*开销*以*GB 秒*为单位。 执行开销是通过将其内存使用率与执行时间相结合来计算的。 用于运行的函数比使用更多内存的函数更长。 

假设函数使用的内存量保持不变。 在这种情况下，计算成本是简单的乘法。 例如，假设函数使用了 0.5 GB，3秒。 则执行开销为 `0.5GB * 3s = 1.5 GB-seconds`。 

由于内存使用量随时间而变化，因此计算实质上是一段时间内内存使用量的整数。  系统会按固定的时间间隔对进程的内存使用量（以及子进程）执行此计算。 如[定价页]中所述，内存使用量向上舍入到最接近的 128-MB 存储桶。 如果你的进程使用 160 MB，则需支付 256 MB。 计算采用并发性，这是同一进程中的多个并发函数执行。

> [!NOTE]
> 尽管在执行开销中不会直接考虑 CPU 使用率，但它可能会影响函数的执行时间。

## <a name="other-related-costs"></a>其他相关成本

估计在任何计划中运行函数的总成本时，请记住，函数运行时使用多个其他 Azure 服务，每个服务都单独计费。 计算 function apps 的定价时，与其他 Azure 服务集成的任何触发器和绑定都要求你为这些附加服务创建并支付费用。 

对于在消耗计划中运行的函数，总成本是函数的执行开销，增加了带宽和其他服务的成本。 

估计函数应用和相关服务的总体成本时，请使用[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=functions)。 

| 相关成本 | 描述 |
| ------------ | ----------- |
| **存储帐户** | 每个函数应用都要求有一个关联的常规用途[Azure 存储帐户](../storage/common/storage-introduction.md#types-of-storage-accounts)，该帐户[单独计费](https://azure.microsoft.com/pricing/details/storage/)。 此帐户由函数运行时在内部使用，但你也可以将其用于存储触发器和绑定。 如果没有存储帐户，则会在创建函数应用时为你创建一个。 若要了解详细信息，请参阅[存储帐户要求](functions-scale.md#storage-account-requirements)。|
| **Application Insights** | 函数依赖于[Application Insights](../azure-monitor/app/app-insights-overview.md)来为函数应用提供高性能的监视体验。 虽然不是必需的，但应[启用 Application Insights 集成](functions-monitoring.md#enable-application-insights-integration)。 每个月都包含遥测数据的免费授予。 若要了解详细信息，请参阅[Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)。 |
| **网络带宽** | 无需为同一区域中的 Azure 服务之间的数据传输付费。 但是，对于到其他区域或 Azure 外部的出站数据传输，可能会产生费用。 若要了解详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。 |

## <a name="behaviors-affecting-execution-time"></a>影响执行时间的行为

函数的以下行为可能会影响执行时间：

+ **触发器和绑定**：从读取输入并将输出写入[函数绑定](functions-triggers-bindings.md)所用的时间将计入执行时间。 例如，当函数使用输出绑定将消息写入到 Azure 存储队列时，执行时间包括将消息写入队列所用的时间，包括在函数开销的计算中。 

+ **异步执行**：函数等待异步请求结果（在中C#为 @no__t）的时间被视为执行时间。 GB-秒计算基于函数的开始和结束时间以及该时间段内的内存使用量。 在这段时间内，不会将 CPU 活动分解为计算。 在异步操作期间，可以使用[Durable Functions](durable/durable-functions-overview.md)降低成本。 在业务流程协调程序函数中等待等待的时间不计费。

## <a name="view-execution-data"></a>查看执行数据

在[您的发票](/azure/billing/billing-download-azure-invoice)中，可以查看所有执行的与成本相关的数据 **-函数**和**执行时间-函数**以及实际计费的成本。 但是，此发票数据是过去发票周期的每月聚合。 

为了更好地了解函数的成本影响，可以使用 Azure Monitor 查看函数应用当前生成的与成本相关的指标。 可以在[Azure 门户]或 REST api 中使用[Azure Monitor 指标资源管理器](../azure-monitor/platform/metrics-getting-started.md)来获取此数据。

### <a name="monitor-metrics-explorer"></a>监视指标资源管理器

使用[Azure Monitor 指标资源管理器](../azure-monitor/platform/metrics-getting-started.md)以图形格式查看消耗计划函数应用的成本相关数据。 

1. 在搜索 "服务"、"**资源" 和 "文档**" 的[Azure 门户]顶部，搜索 @no__t 并选择 "**服务**" 下的 "**监视**"。

1. 在左侧，选择 "**指标**"  > **选择一个资源**，然后使用图像下方的设置选择 function app。

    ![选择 function app 资源](media/functions-consumption-costing/select-a-resource.png)

      
    |设置  |建议的值  |描述  |
    |---------|---------|---------|
    | 订阅    |  订阅  | 包含 function app 的订阅。  |
    | 资源组     | 你的资源组  | 包含 function app 的资源组。   |
    | 资源类型     |  应用服务 | 函数应用显示为监视器中的应用服务实例。 |
    | Resource     |  函数应用  | 要监视的函数应用。        |

1. 选择 "**应用**"，选择要监视的函数应用。

1. 对于 "**度量值**"，选择 "**函数执行计数**" 和 "**聚合** **总计**"。 这会将所选时间段内执行计数的总和添加到图表中。

    ![定义要添加到图表中的函数应用指标](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. 选择 "**添加度量值**"，然后重复步骤2-4，将**函数执行单元**添加到图表中。 

生成的图表包含所选时间范围内两个执行度量值的总和，在本例中为2小时。

![函数执行计数和执行单元的关系图](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

由于执行单元数量大于执行计数，因此图表只显示执行单元。

此图显示了在两个小时内使用的总 1110000000 `Function Execution Units`，以 MB 为单位。 若要转换为 GB 秒，请除以1024000。 在此示例中，函数应用的使用 @no__t 为 0 GB。 您可以采用此值并乘以 "[函数定价" 页][定价页]上的当前执行时间价格，这将为您提供两个小时的成本，假定您已使用任何免费的执行时间授予。 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/)包含用于检索度量值的命令。 可以从本地命令环境使用 CLI，也可以直接从门户使用[Azure Cloud Shell](../cloud-shell/overview.md)。 例如，以下[az monitor 公制 list](/cli/azure/monitor/metrics#az-monitor-metrics-list)命令将返回以前使用的相同时间段内的每小时数据。

请确保将 `<AZURE_SUBSCRIPTON_ID>` 替换为运行命令的 Azure 订阅 ID。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

此命令返回类似于以下示例的 JSON 有效负载：

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
此特定响应显示，从 `2019-09-11T21:46` 到 `2019-09-11T23:18`，其中应用使用 1110000000 MB-毫秒（1083.98 GB-秒）。

## <a name="determine-memory-usage"></a>确定内存使用情况

函数执行单位是执行时间与内存使用情况的组合，这使得它成为了解内存使用的难以理解的指标。 内存数据不是当前通过 Azure Monitor 提供的指标。 但是，如果想要优化应用程序的内存使用情况，可以使用 Application Insights 收集的性能计数器数据。  

如果尚未执行此操作，请[在函数应用中启用 Application Insights](functions-monitoring.md#enable-application-insights-integration)。 启用此集成后，可以[在门户中查询此遥测数据](functions-monitoring.md#query-telemetry-data)。  

在 "**监视**" 下，选择 "**日志（分析）** "，复制以下遥测查询并将其粘贴到查询窗口中，然后选择 "**运行**"。 此查询返回每个采样时间的总内存使用量。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

结果类似于以下示例：

| timestamp \[UTC @ no__t-1          | name          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019，1：05： 14 @ NO__T-0947 AM | 专用字节 | 209932288 |
| 9/12/2019，1：06： 14 @ NO__T-0994 AM | 专用字节 | 212189184 |
| 9/12/2019，1：06： 30 @ NO__T-0010 AM | 专用字节 | 231714816 |
| 9/12/2019，1：07： 15 @ NO__T-0040 AM | 专用字节 | 210591744 |
| 9/12/2019，1：12： 16 @ NO__T-0285 AM | 专用字节 | 216285184 |
| 9/12/2019，1：12： 31 @ NO__T-0376 AM | 专用字节 | 235806720 |

## <a name="function-level-metrics"></a>函数级指标

Azure Monitor 在资源级别跟踪指标，函数是函数应用。 Application Insights 集成基于每个函数发出指标。 下面是一个示例分析查询，用于获取函数的平均持续时间：

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 @ no__t-0087                     |
| QueueTrigger MaxDurationMs | 90 @ no__t-0249                     |
| QueueTrigger MinDurationMs | 8 @ no__t-0522                      |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关监视 function apps 的详细信息](functions-monitoring.md)

[定价页]: https://azure.microsoft.com/pricing/details/functions/
[Azure 门户]: https://portal.azure.com
