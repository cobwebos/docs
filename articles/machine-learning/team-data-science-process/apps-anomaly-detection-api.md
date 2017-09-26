---
title: "Azure 机器学习异常情况检测 API | Microsoft 文档"
description: "异常情况检测 API 是一个示例，它使用 Microsoft Azure 机器学习，检测时序数据和均匀分布在时间中的数值的异常情况。"
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok;rotimpe
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cd7dab8514b41d930d01fd134229cc9da48b18fe
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="machine-learning-anomaly-detection-api"></a>机器学习异常情况检测 API
## <a name="overview"></a>概述
[异常情况检测 API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) 是一个示例，使用 Azure 机器学习构建，检测时序数据和均匀分布在时间中的数字值的异常情况。

此 API 可以检测时序数据中以下类型的异常模式：

* **正值和负值趋势**：例如，监视计算向上趋势过程中的内存使用率可能会很有趣，因为可能会显示内存泄漏，
* **值在动态范围内的更改**：例如，在监视云服务引发的异常情况时，值在动态范围内的任何更改可能表示该服务的运行状况不稳定，
* **峰值和低值**：例如，监视服务中的登录失败次数或电子商务网站中的签出数，其峰值和 dip 可以预测非正常行为。

这些机器学习检测程序跟踪值的更改，并将正在进行的更改报告为异常分数。 它们不需要临时阈值优化，分数可用于控制误报率。 API 在几种情形中非常有用：通过跟踪 KPI 的服务监视、通过度量值（如大量搜索、点击）监视使用情况和通过计数器（如内存、CPU、文件读取等）监视性能。

异常检测产品附带帮助用户入门的有用工具。

* [Web 应用程序](http://anomalydetection-aml.azurewebsites.net/)可帮助评估和可视化数据上的异常情况检测 API。

> [!NOTE]
> 请尝试由 [此 API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) 提供的“IT 异常 Insights 解决方案”
> 
> 若要获取部署到 Azure 订阅的端到端解决方案 <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**从这里开始>**</a>
> 
>

## <a name="api-deployment"></a>API 部署
要使用 API，必须将其部署到 Azure 订阅，在该订阅中它将作为 Azure 机器学习 Web 服务进行托管。  可以从 [Cortana Intelligence 库](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)执行该操作。  这会将两个 AzureML Web 服务（及其相关资源）部署到 Azure 订阅 - 一个用于异常情况检测（包含季节性检测），另一个不包含季节性检测。  部署完成后，便能从 [AzureML Web 服务](https://services.azureml.net/webservices/)页管理 API。  在该页中，能够查找终结点位置、API 密钥以及调用 API 的示例代码。  [此处](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-manage-new-webservice)提供了更详细的说明。

## <a name="scaling-the-api"></a>缩放 API
默认情况下，部署将使用一个免费的开发/测试计费计划，其中包括 1,000 次交易/月和 2 个计算小时/月。  可以根据需求升级到其他计划。  在[此处](https://azure.microsoft.com/en-us/pricing/details/machine-learning/)的“生产 Web API 定价”下提供了有关不同计划的定价的详细信息。

## <a name="managing-aml-plans"></a>管理 AML 计划 
可以在[此处](https://services.azureml.net/plans/)管理计费计划。  计划名称将基于在部署 API 时选择的资源组名称，再加上一个订阅所独有的字符串。  在[此处](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-manage-new-webservice)的“管理计费计划”部分下提供了有关如何升级计划的说明。

## <a name="api-definition"></a>API 定义
该 Web 服务提供 HTTPS 上基于 REST 的 API，可以不同方式使用该 API，包括 Web 或移动应用程序、R、Python、Excel 等。可将时间系列数据通过 REST API 调用发送到此服务，此服务将运行下面介绍的三种异常类型的组合。

## <a name="calling-the-api"></a>调用 API
若要调用该 API，需要知道终结点位置和 API 密钥。  这两项以及调用该 API 的示例代码可从 [AzureML Web 服务](https://services.azureml.net/webservices/)页获得。  导航到所需的 API，并单击“使用”选项卡可找到它们。  请注意，可以像调用 Swagger API 一样调用该 API（即，使用 URL 参数 `format=swagger`），也可以像调用非 Swagger API 一样调用该 API（即，不使用 `format` URL 参数）。  代码示例使用 Swagger 格式。  下面是采用非 Swagger 格式的示例请求和响应。  这些示例针对季节性终结点。  非季节性终结点与它类似。

### <a name="sample-request-body"></a>示例请求正文
该请求包含两个对象：`Inputs` 和 `GlobalParameters`。  在下面的示例请求中，某些参数是显式发送的，而其他参数则不是（向下滚动可找到每个终结点的完整参数列表）。  未在请求中显式发送的参数将使用下面给出的默认值。

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>示例响应
请注意，若要查看 `ColumnNames` 字段，必须在请求中包含 `details=true` 作为 URL 参数。  请参阅下表，了解下述每个字段背后的含义。

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>Score API
Score API 用于运行非季节性时序数据的异常情况检测。 Score API 对数据运行大量异常检测程序，并返回其异常分数。 下图显示 Score API 可检测到的异常情况示例。 此时间序列具有 2 个不同级别的更改和 3 个峰值。 红点显示检测到的更改级别的时间，而黑色的点显示检测到的峰值。
![Score API][1]

### <a name="detectors"></a>检测程序
异常情况检测 API 支持检测程序中的 3 个主要类别。 可在下表中找到特定输入的参数和每个检测程序输出结果的详细信息。

| 检测程序类别 | 检测程序 | 说明 | 输入参数 | Outputs |
| --- | --- | --- | --- | --- |
| 峰值检测程序 |TSpike 检测程序 |根据值与第一个和第三个四分位数的距离，检测峰值和低值 |*tspikedetector.sensitivity：*采用 1-10 中的整数值、默认值：3；较高的值会捕获多个极值，从而使敏感度降低 |TSpike：二进制值 – 如果检测到峰值或低值，显示“1”，否则为“0” |
| 峰值检测程序 | ZSpike 检测程序 |根据数据点与平均值的距离，检测峰值和低值 |*tspikedetector.sensitivity：*采用 1-10 范围内的整数值，默认值为 3；值越大，捕获的值更极端，使敏感程度降低 |TSpike：二进制值 – 如果检测到峰值或低值，显示“1”，否则为“0” | |
| 慢速趋势检测程序 |慢速趋势检测程序 |根据每组的敏感度，检测慢速正面发展趋势 |*trenddetector.sensitivity：*检测程序分数上的阈值（默认值：3.25，3.25 – 5 是合理的选择范围；值越高敏感性越低） |tscore：浮动数字，表示趋势的异常分数 |
| 级别更改检测程序 | 双向级别更改检测程序 |根据每组的敏感度，检测向下和向上级别的更改 |*bileveldetector.sensitivity：*检测程序分数上的阈值（默认值：3.25，3.25 – 5 是合理的选择范围；值越高敏感性越低） |rpscore：浮动数字，表示向上和向下级别更改的异常分数 | |

### <a name="parameters"></a>parameters
下表中列出了这些输入参数的更多详细信息：

| 输入参数 | 说明 | 默认设置 | 类型 | 有效的范围 | 推荐的区域 |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |用于记录异常分数计算结果（在数据点的 #） |500 |integer |10-2000 |时间序列依赖项 |
| detectors.spikesdips | 是仅检测峰值、仅检测 dip，还是两者都检测 |两者 |枚举 |两者、峰值、Dip |两者 |
| bileveldetector.sensitivity |双向级别更改检测程序的敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| 趋势检测程序敏感度 |正面发展趋势检测程序敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| tspikedetector.sensitivity |TSpike 检测程序的敏感度 |3 |integer |1-10 |3-5（值越小，敏感度越高） |
| zspikedetector.sensitivity |ZSpike 检测程序的敏感度 |3 |integer |1-10 |3-5（值越小，敏感度越高） |
| postprocess.tailRows |保留输出结果中的最新数据点的数量 |0 |integer |0（保留所有数据点），或指定要保留在结果中的点的数量 |不适用 |

### <a name="output"></a>输出
API 在时间系列数据上运行所有检测程序，并及时返回异常的分数以及每个点的二进制峰值指示符。 下表列出了 API 的输出内容。 

| Outputs | 说明 |
| --- | --- |
| 时间 |应用聚合（和/或）缺失数据时，原始数据或聚合（和/或）数据估算中的时间戳 |
| 数据 |应用聚合（和/或）缺失数据时，原始数据或聚合（和/或）数据估算中的值 |
| TSpike |二进制指示符指示 TSpike 检测程序是否检测到峰值 |
| ZSpike |二进制指示符指示 ZSpike 检测程序是否检测到峰值 |
| rpscore |浮动数字表示双向级别更改上的异常分数 |
| rpalert |基于输入敏感度，指示存在双向级别更改异常的 1/0 值 |
| tscore |浮动数字表示正面趋势的更改异常 |
| talert |1/0 值指示存在基于输入敏感度的、正面趋势异常 |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality API 用于对具有季节性模式的时序运行异常情况检测。 此 API 可用于检测季节性模式中的偏差。  
下图显示在季节性时序中检测到的异常情况示例。 时间序列都有一个最大值（第一个黑点）、两个低值（第二个黑点和结尾处的黑点）和一个级别更改（红点）。 请注意：从序列中删除季节性组件后才可识别时间序列中间的低值和级别更改。
![Seasonality API][2]

### <a name="detectors"></a>检测程序
季节性终结点中的检测器类似于非季节性终结点中的检测器，但使用的参数名称（如下所列）略有不同。

### <a name="parameters"></a>parameters

下表中列出了这些输入参数的更多详细信息：

| 输入参数 | 说明 | 默认设置 | 类型 | 有效的范围 | 推荐的区域 |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |聚合输入时间序列的聚合间隔单位为秒 |0（不执行任何聚合） |integer |0：跳过聚合，> 0 否则 |5 分钟到 1 天，时间系列依赖项 |
| preprocess.aggregationFunc |用于将数据聚合到指定的 AggregationInterval 函数 |平均值 |枚举 |平均值、总和、长度 |不适用 |
| preprocess.replaceMissing |用户输入缺失的数据值 |lkv（最近一次的值） |枚举 |零、lkv、平均值 |不适用 |
| detectors.historyWindow |用于记录异常分数计算结果（在数据点的 #） |500 |integer |10-2000 |时间序列依赖项 |
| detectors.spikesdips | 是仅检测峰值、仅检测 dip，还是两者都检测 |两者 |枚举 |两者、峰值、Dip |两者 |
| bileveldetector.sensitivity |双向级别更改检测程序的敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| postrenddetector.sensitivity |正面发展趋势检测程序敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| negtrenddetector.sensitivity |负面发展趋势检测程序敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| tspikedetector.sensitivity |TSpike 检测程序的敏感度 |3 |integer |1-10 |3-5（值越小，敏感度越高） |
| zspikedetector.sensitivity |ZSpike 检测程序的敏感度 |3 |integer |1-10 |3-5（值越小，敏感度越高） |
| seasonality.enable |是否要执行季节性分析 |是 |布尔值 |true、false |时间序列依赖项 |
| seasonality.numSeasonality |检测到的定期周期的最大数量 |1 |integer |1, 2 |1-2 |
| seasonality.transform |是否应在应用异常检测前删除季节性（和）趋势组件 |deseason |枚举 |none, deseason, deseasontrend |不适用 |
| postprocess.tailRows |保留输出结果中的最新数据点的数量 |0 |integer |0（保留所有数据点），或指定要保留在结果中的点的数量 |不适用 |

### <a name="output"></a>输出
API 在时间系列数据上运行所有检测程序，并及时返回异常的分数以及每个点的二进制峰值指示符。 下表列出了 API 的输出内容。 

| Outputs | 说明 |
| --- | --- |
| 时间 |应用聚合（和/或）缺失数据时，原始数据或聚合（和/或）数据估算中的时间戳 |
| 原始数据 |应用聚合（和/或）缺失数据时，原始数据或聚合（和/或）数据估算中的值 |
| ProcessedData |执行下列操作之一： <ul><li>如果检测到重大季节性变化并选中非季节性选项，随季节波动调整时间序列；</li><li>如果检测到重大季节性变化并选中非季节性选项，随季节波动调整时间序列，并去除其趋势</li><li>否则，与 OriginalData 相同</li> |
| TSpike |二进制指示符指示 TSpike 检测程序是否检测到峰值 |
| ZSpike |二进制指示符指示 ZSpike 检测程序是否检测到峰值 |
| BiLevelChangeScore |浮动数字，表示级别更改的异常分数 |
| BiLevelChangeAlert |1/0 值，指示存在基于输入敏感度的、级别更改异常 |
| PosTrendScore |浮动数字表示正面趋势的更改异常 |
| PosTrendAlert |1/0 值指示存在基于输入敏感度的、正面趋势异常 |
| NegTrendScore |浮动数字，表示负面趋势的异常分数 |
| NegTrendAlert |1/0 值，指示存在基于输入敏感度的、负面趋势异常 |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png


