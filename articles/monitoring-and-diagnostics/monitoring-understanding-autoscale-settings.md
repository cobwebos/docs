---
title: 了解 Azure Monitor 中的自动缩放设置
description: 自动缩放设置的详细步骤及其工作原理。 适用于虚拟机、云服务、Web 应用
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 982bc43fd86a808da07833d77bde17e17789b2d6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264990"
---
# <a name="understand-autoscale-settings"></a>了解自动缩放设置
使用自动缩放设置有助于确保运行适当数量的资源来处理应用程序负载的波动。 可将自动缩放设置配置为基于指标（指示负载或性能）触发，或者在计划好的日期和时间触发。 本文将会深度剖析自动缩放设置。 本文首先介绍设置的架构和属性，然后逐步讲解可配置的不同配置文件类型。 最后讨论 Azure 中的自动缩放功能如何评估要在任意给定时间执行哪个配置文件。

## <a name="autoscale-setting-schema"></a>自动缩放设置的架构
为了演示自动缩放设置的架构，本文使用了以下自动缩放设置。 请务必注意，此自动缩放设置包含：
- 一个配置文件。 
- 此配置文件包含两条指标规则：一条规则针对扩展，另一条规则针对缩减。
  - 当虚拟机规模集的平均 CPU 百分比指标在过去 10 分钟大于 85% 时，将触发扩展规则。
  - 当虚拟机规模集的平均值在过去 1 分钟小于 60% 时，将触发缩减规则。

> [!NOTE]
> 一项设置可以包含多个配置文件。 有关详细信息，请参阅[配置文件](#autoscale-profiles)部分。 还可以在一个配置文件中定义多个扩展规则和缩减规则。 有关评估方法，请参阅[评估](#autoscale-evaluation)部分。

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| 部分 | 元素名称 | 说明 |
| --- | --- | --- |
| 设置 | ID | 自动缩放设置的资源 ID。 自动缩放设置属于 Azure 资源管理器资源。 |
| 设置 | 名称 | 自动缩放设置的名称。 |
| 设置 | location | 自动缩放设置的位置。 此位置可与缩放的资源所在的位置不同。 |
| 属性 | targetResourceUri | 缩放的资源的资源 ID。 针对每个资源，只能使用一项自动缩放设置。 |
| 属性 | 配置文件 | 自动缩放设置由一个或多个配置文件组成。 自动缩放引擎每次运行时，将会执行一个配置文件。 |
| 个人资料 | 名称 | 配置文件的名称。 可以选择有助于识别该配置文件的任何名称。 |
| 个人资料 | Capacity.maximum | 允许的最大容量。 此值可以确保自动缩放在执行此配置文件时，不会将资源扩展到超过此数字。 |
| 个人资料 | Capacity.minimum | 允许的最小容量。 此值可以确保自动缩放在执行此配置文件时，不会将资源缩减到低于此数字。 |
| 个人资料 | Capacity.default | 如果读取资源指标（在本例中，为“vmss1”的 CPU）时出现问题，且当前容量低于默认容量，则自动缩放将扩展到默认值。 这是为了确保资源可用性。 如果当前容量已大于默认容量，则自动缩放不会缩减。 |
| 个人资料 | 规则 | 自动缩放使用配置文件中的规则在最大和最小容量之间自动缩放。 可以在一个配置文件中包含多个规则。 通常包含两个规则：一个用于确定何时扩展，另一个用于确定何时缩减。 |
| 规则 | metricTrigger | 定义规则的指标条件。 |
| metricTrigger | metricName | 指标的名称。 |
| metricTrigger |  metricResourceUri | 发出指标的资源的资源 ID。 在大多数情况下，它与缩放的资源相同。 在某些情况下，它可能不同。 例如，可以基于存储队列中的消息数缩放虚拟机规模集。 |
| metricTrigger | timeGrain | 指标采样持续时间。 例如，**TimeGrain = "PT1M"** 表示应使用 statistic 元素中指定的聚合方法每分钟聚合一次指标。 |
| metricTrigger | statistic | timeGrain 时间段内的聚合方法。 例如，**statistic = "Average"** 且 **timeGrain = "PT1M"** 表示每分钟取平均值来聚合指标。 此属性规定指标的采样方式。 |
| metricTrigger | timeWindow | 查找指标的时间范围。 例如，**timeWindow = "PT10M"** 表示自动缩放每次运行时，都会查询过去 10 分钟的指标。 使用该时间范围可将指标规范化，避免对暂时性的峰值作出反应。 |
| metricTrigger | timeAggregation | 用于聚合已采样指标的聚合方法。 例如，如果 **TimeAggregation = "Average"**，则应取平均值来聚合采样的指标。 上例取 10 个 1 分钟样本并求其平均值。 |
| 规则 | scaleAction | 触发规则的 metricTrigger 时要执行的操作。 |
| scaleAction | direction | “Increase”表示扩展，“Decrease”表示缩减。|
| scaleAction | 值 | 要将资源容量增大或减小多少。 |
| scaleAction | cooldown | 在执行缩放操作之后、再次执行缩放操作之前所要等待的时间。 例如，如果 **cooldown = "PT10M"**，则自动缩放只会在 10 分钟之后才尝试再次执行缩放。 在添加或删除实例之后，cooldown（冷却）可让指标变稳定。 |

## <a name="autoscale-profiles"></a>自动缩放配置文件

有三种类型的自动缩放配置文件：

- **常规配置文件：** 最常见的配置文件。 如果不需要根据星期日期或特定的日历日期以不同的方式缩放资源，则可以使用常规配置文件。 然后，可以使用规定何时扩展及何时缩减的指标规则，来配置此配置文件。 只应定义一个常规配置文件。

    本文前面使用的示例配置文件就是一个常规配置文件。 请注意，还可以将配置文件设置为缩放到资源的静态实例计数。

- **固定日期配置文件：** 此配置文件适用于特殊场合。 假设你要在 2017 年 12 月 26 日（太平洋标准时间）举办一场重要活动。 你希望当天的最小和最大资源容量有所不同，但仍可根据相同的指标缩放。 在这种情况下，应在设置的配置文件列表中添加一个固定日期配置文件。 该配置文件配置为只在活动当天运行。 在其他任何日期，自动缩放将使用常规配置文件。

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **重复配置文件：** 使用此类配置文件可以确保始终在特定的星期日期使用此配置文件。 重复配置文件只包含开始时间。 它们会一直运行到下一个重复配置文件或固定日期配置文件设置为启动为止。 只包含一个重复配置文件的自动缩放设置只会运行该配置文件，即使相同的设置中定义了常规配置文件。 以下两个示例演示了此配置文件的用法：

    **示例 1：工作日与周末**
    
    假设你希望周末的最大容量为 4。 在工作日，由于预期的负载更大，你希望最大容量为 10。 在这种情况下，可在设置中包含两个重复配置文件，一个在周末运行，另一个在工作日运行。
    设置如下所示：

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    上面的设置显示，每个重复配置文件都包含一个计划。 此计划确定配置文件开始运行的时间。 需要运行另一个配置文件时，上述配置文件会停止执行。

    例如，在上面的设置中，“weekdayProfile”设置为在星期一午夜 12 点启动。 这意味着，此配置文件将在星期一午夜 12 点开始运行。 它会持续到星期六午夜 12 点，即“weekendProfile”计划开始运行的时间。

    **示例 2 - 营业时间**
    
    假设你要在营业时间上午 9 点到下午 5 点使用一个指标阈值，并其他所有时间使用另一个指标阈值。 该设置如下所示：
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    在上面的设置中，“businessHoursProfile”在星期一上午 9 点开始运行，并持续到下午 5 点。 下午 5 点是“nonBusinessHoursProfile”开始运行的时间。 “nonBusinessHoursProfile”运行到星期二上午 9 点，然后接着运行“businessHoursProfile”。 此配置文件重复到星期五下午 5 点。 然后，“nonBusinessHoursProfile”一直运行到星期一上午 9 点。
    
> [!Note]
> Azure 门户中的自动缩放用户界面针对重复配置文件强制实施结束时间，在重复配置文件之间开始运行自动缩放设置的默认配置文件。
    
## <a name="autoscale-evaluation"></a>自动缩放评估
由于自动缩放设置可以包含多个配置文件，并且每个配置文件可以包含多个指标规则，因此，必须了解如何评估自动缩放设置。 每当自动缩放作业运行时，首先会选择适用的配置文件。 然后，自动缩放将评估该配置文件中的最小、最大值和任何指标规则，并确定是否需要执行缩放操作。

### <a name="which-profile-will-autoscale-pick"></a>自动缩放会选择哪个配置文件？

自动缩放根据以下顺序选择配置文件：
1. 首先查找配置为立即运行的任何固定日期配置文件。 如果有此类文件，则自动缩放会运行该配置文件。 如果存在多个需要运行的固定日期配置文件，自动缩放将选择第一个。
2. 如果不存在固定日期配置文件，自动缩放会查找重复配置文件。 如果找到了重复配置文件，则运行它。
3. 如果没有固定日期配置文件或重复配置文件，则自动缩放会运行常规配置文件。

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>自动缩放如何评估多个规则？

自动缩放确定要运行哪个配置文件之后，会评估该配置文件中的所有扩展规则（设置为 **direction = “Increase”** 的规则）。

如果触发了一个或多个扩展规则，自动缩放将计算其中每个规则的 **scaleAction** 确定的新容量。 然后，它会扩展到这些容量的最大值，以确保服务可用性。

例如，假设某个虚拟机规模集的当前容量为 10。 有两条扩展规则：一个规则将容量增大 10%，另一个将容量增大 3%。 第一条规则会导致新容量更改为 11，第二条规导致容量更改为 13。 为了确保服务可用性，自动缩放会选择可以产生最大容量的操作，因此选择了第二条规则。

如果未触发扩展规则，自动缩放会评估所有缩减规则（设置为 **direction = “Decrease”** 的规则）。 如果触发了所有缩减规则，自动缩放只会执行某个缩减操作。

自动缩放计算其中每个规则的 **scaleAction** 确定的新容量。 然后，选择可产生其中最大容量的缩放操作，以确保服务可用性。

例如，假设某个虚拟机规模集的当前容量为 10。 有两条缩减规则：一个规则将容量减小 50%，另一个将容量减小 3%。 第一条规则会导致新容量更改为 5，第二条规导致容量更改为 7。 为了确保服务可用性，自动缩放会选择可以产生最大容量的操作，因此选择了第二条规则。

## <a name="next-steps"></a>后续步骤
若要详细了解自动缩放，请参阅以下文章：

* [自动缩放概述](monitoring-overview-autoscale.md)
* [Azure 监视器自动缩放常用指标](insights-autoscale-common-metrics.md)
* [Azure 监视器自动缩放的最佳做法](insights-autoscale-best-practices.md)
* [使用自动缩放操作发送电子邮件和 webhook 警报通知](insights-autoscale-to-webhook-email.md)
* [自动缩放 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
