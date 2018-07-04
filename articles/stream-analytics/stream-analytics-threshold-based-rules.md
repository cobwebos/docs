---
title: 处理 Azure 流分析中基于可配置阈值的规则
description: 本文介绍如何使用参考数据实现在 Azure 流分析中拥有基于可配置阈值的规则的警报解决方案。
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 802be1ad5b1029add249430ee7760002407c4641
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021619"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>处理 Azure 流分析中基于可配置阈值的规则
本文介绍如何使用参考数据实现在 Azure 流分析中使用基于可配置阈值的规则的警报解决方案。

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>方案：基于可调整规则阈值的警报
当传入的流式处理事件达到特定值，或者当基于传入的流式处理事件的聚合值超过特定阈值时，可能需要生成警报作为输出。 设置比较该值与预先确定的固定静态阈值的流分析查询非常简单。 固定阈值可硬编码为使用简单数值比较（大于、小于和等于）的流式处理查询语法。

在某些情况下，需要能够更加轻松地配置阈值，确保无需在每次阈值发生更改时编辑查询语法。 在其他情况下，同一个查询可能需要处理多个设备或用户，并且每个查询需要在每种设备上设置不同的阈值。 

此模式可用于动态配置阈值，也可以通过筛选输入数据有选择性地选择阈值所应用到的设备类型，还可以有选择性地选择要包含在输出中的字段。

## <a name="recommended-design-pattern"></a>建议的设计模式
将参考数据输入用于流分析作业，以查找警报阈值：
- 将阈值存储在参考数据中，一个键一个值。
- 将流式处理数据输入事件联接到键列上的参考数据。
- 将参考数据中的键控值用作阈值。

## <a name="example-data-and-query"></a>示例数据和查询
在示例中，当在一分钟的窗口内从设备流式传入的数据的聚合与规则中作为参考数据提供的规定值匹配时，生成警报。

在查询中，对于每个 deviceId 和每个 deviceId 下的 metricName，可以从 0 到 5 个维度进行配置，以作为分组依据。 仅对具有相应筛选值的事件进行分组。 分组后，Min、Max、Avg 的窗口聚合以 60 秒的翻转窗口进行计算。 然后，根据参考数据中配置的阈值筛选计算出的聚合值，以生成警报输出事件。

例如，假设有一个流分析作业，它有一个名为“rules”的参考数据输入和一个名为“metrics”的流式处理数据输入。 

## <a name="reference-data"></a>引用数据
此示例参考数据显示基于阈值的规则的表示形式。 JSON 文件保存参考数据并保存在 Azure blob 存储中，该 blob 存储容器用作名为“rules”的参考数据输入。 随着时间的推移，用户可以覆盖此 JSON 文件并替换规则配置，而无需停止或启动流式处理作业。

- 示例规则用于表示在 CPU 超出（平均数大于或等于）`90`% 时的可调整警报。 `value` 字段可根据需要进行配置。
- 注意，规则有“operator”字段，稍后将在有关 `AVGGREATEROREQUAL` 上的查询语法中动态阐释。 
- 规则筛选键/值对为 `2`/`C1` 的特定维度的数据。 其他字段为空字符串，指示不会按这些事件字段筛选输入流。 用户可以设置其他 CPU 规则来根据需要筛选其他匹配字段。
- 并非所有列都包含在输出警报事件中。 在本例中，`includedDim` 键号 `2` 打开为 `TRUE`，表示流中事件数据的字段号 2 将包含在符合条件的输出事件中。 其他字段不包含在警报输出中，但可以调整字段列表。


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>示例流式处理查询
此示例流分析查询将上列中的“rules”参考数据联接到名为“metrics”上的数据的输入流。

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>示例流式处理输入事件数据
此示例 JSON 数据表示上述流式处理查询中使用的“metrics”输入数据。 

- 在 1 分钟内列出三个示例事件，值为 `T14:50`。 
- 这三个示例事件的 `deviceId` 值都为 `978648`。
- 每个事件的 CPU 指标值各不相同，分别为 `98`、`95`、`80`。 仅前两个示例事件超过规则中确定的 CPU 警报规则。
- 警报规则中的 includeDim 字段为键号 2。 示例事件中的对应键 2 字段名为 `NodeName`。 这三个示例事件的值分别为 `N024`、`N024` 和 `N014`。 在输出中，只能看见节点 `N024`，因为它是唯一满足高 CPU 警报条件的数据。 `N014` 不满足高 CPU 阈值。
- 警报规则仅在键号 2 上配置 `filter`，这对应示例事件中的 `cluster` 字段。 这三个示例事件都有值 `C1` 并满足筛选条件。

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>示例输出
此示例输出 JSON 数据显示，单个警报事件是基于参考数据中定义的 CPU 阈值规则生成的。 输出事件包含警报名称以及涉及字段的聚合（平均值、最小值、最大值）。 鉴于规则配置，输出事件数据包括字段键号 2 `NodeName` 值 `N024`。 （将 JSON 更改为显示换行符以便阅读。）

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```