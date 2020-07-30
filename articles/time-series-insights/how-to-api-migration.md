---
title: 迁移到新的 Azure 时序见解 Gen2 API 版本 |Microsoft Docs
description: 如何更新 Azure 时序见解 Gen2 环境以使用新的公开发布版本。
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: shresha
ms.openlocfilehash: a74a5e2b8e80121324dc8b880d90f493d5b2ddfd
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423943"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>迁移到新的 Azure 时序见解 Gen2 API 版本

## <a name="overview"></a>概述

如果你在公开预览版（2020年7月16日之前）创建了 Azure 时序见解 Gen2 环境，请按照本文中所述的步骤将你的 TSI 环境更新为使用新的已正式发布的 Api 版本。

新的 API 版本为 `2020-07-31` ，并使用更新的时序[表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

用户必须迁移其环境的时序[模型变量](./concepts-variables.md)、保存的查询、Power BI 查询，以及调用 API 终结点的任何自定义工具。 如果你对迁移过程有任何疑问或疑问，请通过 Azure 门户提交支持票证，并提及本文档。

> [!IMPORTANT]
> 预览版 API 版本在 `2018-11-01-preview` 2020 年10月31日之前将继续受支持。 在此之前，请先完成此迁移的所有适用步骤，以避免服务中断。

## <a name="migrate-time-series-model-and-saved-queries"></a>迁移时序模型和已保存的查询

若要帮助用户迁移其[时序模型变量](./concepts-variables.md)和已保存的查询，可以使用[Azure 时序见解资源管理器](https://insights.timeseries.azure.com)提供的内置工具。 导航到要迁移的环境，然后执行以下步骤。 **你可以部分完成迁移并返回到以后完成，但是，不能还原任何更新。**

> [!NOTE]
> 您必须是环境的参与者，才能更新时序模型并保存查询。 如果你不是参与者，则只能迁移你的个人保存的查询。 请在继续操作之前查看[环境访问策略](./concepts-access-policies.md)和访问级别。

1. 资源管理器会提示你更新时序模型变量和已保存查询所使用的语法。

    [![Prompt](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    如果意外关闭了通知，可以在通知面板中找到该通知。

1. 单击 "**显示更新**" 以打开迁移工具。

1. 单击 "**下载类型**"。 由于迁移将覆盖你的当前类型来更改变量语法，因此你将需要保存当前类型的副本。 当类型已下载时，该工具将通知您。

    [![下载类型](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. 单击 "**更新变量**"。 变量更新后，该工具将通知您。

    > [!IMPORTANT]
    > 如果更新类型，则使用较旧 API 版本（）的自定义应用程序 `2018-11-01-preview` 将需要使用新的 api 版本（ `2020-07-31` ）以继续工作。 如果你不确定所使用的 API 版本，请在更新前咨询你的管理员。 稍后可以关闭迁移工具并返回到这些步骤。 详细了解[如何迁移自定义应用程序](#migrate-custom-applications)。

    [![更新变量](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. 单击 "**更新保存的查询**"。 变量更新后，该工具将通知您。

    [![更新保存的查询](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. 单击“完成”。

    [![已完成迁移](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

通过绘制一些新创建的变量和已保存的查询，来查看更新的环境。 如果图表中出现任何意外行为，请使用资源管理器中的反馈工具向我们发送反馈。

## <a name="migrate-power-bi-queries"></a>迁移 Power BI 查询

如果已使用 Power BI 连接器生成查询，则使用预览版 API 版本和旧时序表达式语法调用 Azure 时序见解。 在弃用预览 API 之前，这些查询将继续成功检索数据。

若要更新查询以使用新的 API 版本和新的时序表达式语法，则需要从资源管理器重新生成查询。 阅读有关如何[使用 Power BI 连接器创建查询](./how-to-connect-power-bi.md)的详细信息。

> [!NOTE]
> 必须使用 Power BI Desktop 7 月2020版。 如果不是，您可能会发现[查询负载版本错误无效](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect)。

## <a name="migrate-custom-applications"></a>迁移自定义应用程序

如果你的自定义应用程序正在调用以下 REST 终结点，则可以将 API 版本更新为 `2020-07-31` URI 中的：

- 时序模型 API
  - 模型设置 Api
    - [获取](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [更新](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - 实例 API
    - [所有批处理操作](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [列表](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [搜索](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [建议](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - 层次结构 Api
    - [所有批处理操作](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [列表](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - 类型 Api
    - [删除，获取操作](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [列表](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

对于以下 REST 终结点，必须在 URI 中将 API 版本更新为， `2020-07-31` 并确保所有出现的属性都 `tsx` 使用更新的[时序表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

- 类型 Api
  - [Put 操作](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- 查询 Api
  - [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>示例

#### <a name="typesbatchput"></a>TypesBatchPut

旧请求正文（由使用 `2018-11-01-preview` ）：

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

更新的请求正文（由使用 `2020-07-31` ）：

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

此外， `filter` 也可以是 `$event.Mode.String = 'outdoor'` 。 `value`必须使用括号来转义特殊字符（ `_` ）。

#### <a name="getevents"></a>GetEvents

旧请求正文（由使用 `2018-11-01-preview` ）：

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

更新的请求正文（由使用 `2020-07-31` ）：

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

此外， `filter` 也可以是 `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` 。

#### <a name="getseries"></a>GetSeries

旧请求正文（由使用 `2018-11-01-preview` ）：

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

更新的请求正文（由使用 `2020-07-31` ）：

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

此外， `value` 也可以是 `$event['Bar-Pressure-Offset'].Double` 。 如果未指定数据类型，则始终假定数据类型为双精度型。 必须使用括号表示法来转义特殊字符（ `-` ）。

#### <a name="aggregateseries"></a>AggregateSeries

旧请求正文（由使用 `2018-11-01-preview` ）：

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

更新的请求正文（由使用 `2020-07-31` ）：

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

此外， `value` 也可以是 `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` 。

### <a name="potential-errors"></a>潜在错误

#### <a name="invalidinput"></a>InvalidInput

如果看到以下错误，则你使用的是新的 API 版本（ `2020-07-31` ），但 TSX 语法尚未更新。 请查看上面的时序[表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)和迁移示例。 重新 `tsx` 提交 API 请求之前，请确保所有属性都已正确更新。

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- 通过[Azure 时序见解资源管理器](./concepts-ux-panels.md)或自定义应用程序测试环境。
