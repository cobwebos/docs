---
title: Azure 流分析中 JavaScript 用户定义的聚合
description: 本文介绍如何在 Azure 流分析中通过 JavaScript 用户定义的聚合执行高级查询机制。
services: stream-analytics
author: minhe-msft
ms.author: minhe
manager: santoshb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 718109d17309747a3c19f22921e4a316b0b88dc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure 流分析 JavaScript 用户定义的聚合（预览）

Azure 流分析支持以 JavaScript 编写的用户定义的聚合 (UDA)，可实现复杂的有状态业务逻辑。 在 UDA 中，我们可以全面控制状态数据结构、状态累积、状态分散和聚合结果计算。 本文介绍两个不同的 JavaScript UDA 接口、UDA 的创建步骤，以及如何在流分析查询中将 UDA 与基于窗口的操作结合使用。

## <a name="javascript-user-defined-aggregates"></a>JavaScript 用户定义的聚合

用户定义的聚合在时间窗口规范的顶层使用，可基于该窗口内的事件进行聚合，并生成单个结果值。 流分析目前支持两种类型的 UDA 接口：AccumulateOnly 和 AccumulateDeaccumulate。 翻转窗口、跳跃窗口和滑动窗口可以使用这两种类型的 UDA。 与跳跃窗口和滑动窗口结合使用时，AccumulateDeaccumulate UDA 的表现比 AccumulateOnly UDA 更好。 可以根据所用的算法选择其中一种类型。

### <a name="accumulateonly-aggregates"></a>AccumulateOnly 聚合

AccumulateOnly 聚合只能将新事件累积到其状态中，算法不允许将值分散。 无法实现从状态值中分散事件信息时，请选择此聚合类型。 下面是 AccumulatOnly 聚合的 JavaScript 模板：

````JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate 聚合

AccumulateDeaccumulate 聚合允许从状态中分散以前积累的某个值，例如，从事件值列表中删除某个键值对，或者从求和聚合的状态中减去某个值。 下面是 AccumulateDeaccumulate 聚合的 JavaScript 模板：

````JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
````

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript 函数声明

每个 JavaScript UDA 由函数对象声明定义。 下面是 UDA 定义中的主要元素。

### <a name="function-alias"></a>函数别名

函数别名是 UDA 标识符。 在流分析查询中调用时，始终结合“uda”使用 UDA 别名。 前缀开头。

### <a name="function-type"></a>函数类型

对于 UDA 而言，函数类型应为 **Javascript UDA**。

### <a name="output-type"></a>输出类型

流分析作业支持的特定类型；如果想要在查询中处理类型，则值为“Any”。

### <a name="function-name"></a>函数名称

此函数对象的名称。 函数名称在字面上应与 UDA 别名匹配（这是预览版中的行为，我们正考虑在正式版中支持匿名函数）。

### <a name="method---init"></a>方法 - init()

Init() 方法初始化聚合的状态。 窗口启动时会调用此方法。

### <a name="method--accumulate"></a>方法 – accumulate()

Accumulate() 方法基于前一状态和当前事件值计算 UDA 状态。 当某个事件进入时间窗口（TUMBLINGWINDOW、HOPPINGWINDOW 或 SLIDINGWINDOW）时，会调用此方法。

### <a name="method--deaccumulate"></a>方法 – deaccumulate()

deaccumulate() 方法基于前一状态和当前事件值重新计算状态。 当事件退出 SLIDINGWINDOW 时，会调用此方法。

### <a name="method--deaccumulatestate"></a>方法 – deaccumulateState()

deaccumulateState() 方法基于前一状态和跃点状态重新计算状态。 当一组事件退出 HOPPINGWINDOW 时，会调用此方法。

### <a name="method--computeresult"></a>方法 – computeResult()

computeResult() 方法基于当前状态返回聚合结果。 在时间窗口（TUMBLINGWINDOW、HOPPINGWINDOW 或 SLIDINGWINDOW）结束时调用此方法。

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA 支持的输入和输出数据类型
有关 JavaScript UDA 数据类型，请参阅[集成 JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) 的**流分析和 JavaScript 类型转换**部分。

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>通过 Azure 门户添加 JavaScript UDA

下面演练通过门户创建 UDA 的过程。 此处使用的示例计算时间加权平均值。

现在，让我们执行以下步骤在现有的 ASA 作业下创建一个 JavaScript UDA。

1. 登录到 Azure 门户，并找到现有的流分析作业。
1. 然后单击“作业拓扑”下的函数链接。
1. 单击“添加”图标添加新函数。
1. 在“新建函数”视图中，选择“JavaScript UDA”作为函数类型，然后，编辑器中会显示默认的 UDA 模板。
1. 填入“TWA”作为 UDA 别名，并按如下所示更改函数实现：

    ````JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ````

1. 单击“保存”按钮后，该 UDA 会显示在函数列表中。

1. 单击新函数“TWA”，可以检查函数定义。

## <a name="calling-javascript-uda-in-asa-query"></a>在 ASA 查询中调用 JavaScript UDA

在 Azure 门户中打开作业，编辑查询，并调用具有必需前缀“uda.”的 TWA() 函数。 例如：

````SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
````

## <a name="testing-query-with-uda"></a>使用 UDA 测试查询

创建包含以下内容的本地 JSON 文件，将该文件上传到流分析作业，并测试上述查询。

````JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
````

## <a name="get-help"></a>获取帮助

如需更多帮助，请访问我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
