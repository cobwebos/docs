---
title: "机器学习应用：异常情况检测服务 | Microsoft Docs"
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
ms.date: 10/11/2016
ms.author: alokkirpal
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9b900e3febd3ba945250b52ff4b878f22eae33f3


---
# <a name="machine-learning-anomaly-detection-service"></a>机器学习异常情况检测服务
## <a name="overview"></a>概述
[异常情况检测 API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) 是一个示例，使用 Azure 机器学习构建，检测时序数据和均匀分布在时间中的数字值的异常情况。 

此 API 可以检测时序数据中以下类型的异常模式：

* **正值和负值趋势**：例如，监视计算向上趋势过程中的内存使用率可能会很有趣，因为可能会显示内存泄漏，
* **值在动态范围内的更改**：例如，在监视云服务引发的异常情况时，值在动态范围内的任何更改可能表示该服务的运行状况不稳定，
* **峰值和低值**：例如，监视服务中的登录失败次数或电子商务网站中的签出数，其峰值和 dip 可以预测非正常行为。

这些机器学习检测程序跟踪值的更改，并将正在进行的更改报告为异常分数。 它们不需要临时阈值优化，分数可用于控制误报率。 API 在几种情形中非常有用：通过跟踪 KPI 的服务监视、通过度量值（如大量搜索、点击）监视使用情况和通过计数器（如内存、CPU、文件读取等）监视性能。

异常检测产品附带帮助用户入门的有用工具。 

* [Web 应用程序](http://anomalydetection-aml.azurewebsites.net/)可帮助评估和可视化数据上的异常情况检测 API。 
* [示例代码](http://adresultparser.codeplex.com/)演示如何以编程方式访问 API 和 C# 形式的结果。

> [!NOTE]
> 请尝试由 [此 API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) 提供的“IT 异常 Insights 解决方案”
> 
> 若要将此端到端解决方案部署到 Azure 订阅，请<a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**从此处开始 >**</a>
> 
> 

## <a name="api-definition"></a>API 定义
该服务提供通过 HTTPS 基于 REST 的 API，可采用不同方式使用该 API，包括 Web 或移动应用程序、R、Python、Excel 等。将时间系列数据通过 REST API 调用发送到此服务，运行上面介绍的三个异常类型的组合。 该服务在 Azure 机器学习平台上运行，可无缝地扩展到业务需求，并提供 SLA。

### <a name="headers"></a>标头
确保请求包含正确的标头，如下所示：

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

在 [Azure 数据市场](https://datamarket.azure.com/account/keys)中，可以从帐户找到帐户密钥。 

### <a name="score-api"></a>Score API
Score API 用于运行非季节性时序数据的异常情况检测。 Score API 对数据运行大量异常检测程序，并返回其异常分数。 下图显示 Score API 可检测到的异常情况示例。 此时间序列具有 2 个不同级别的更改和 3 个峰值。 红点显示检测到的更改级别的时间，而黑色的点显示检测到的峰值。

![Score API][1]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**示例请求正文**

在下面的请求中，将时序（显示被截断）、2016 年 1 月 3 日到2016 年 10 月 3 日间的数据点和峰值检测程序的参数发送到 API，以便检测这些数据点是否存在异常情况。 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

对此的响应会是： 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

### <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
ScoreWithSeasonality API 用于对具有季节性模式的时序运行异常情况检测。 此 API 可用于检测季节性模式中的偏差。  

下图显示在季节性时序中检测到的异常情况示例。 时间序列都有一个最大值（第一个黑点）、两个低值（第二个黑点和结尾处的黑点）和一个级别更改（红点）。 请注意：从序列中删除季节性组件后才可识别时间序列中间的低值和级别更改。

![Seasonality API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**示例请求正文**

在下面的请求中，将时序（显示被截断）、2016 年 1 月 3 日到2016 年 10 月 3 日的数据点和参数发送到 API，以便检测这些数据点存在否异常情况。 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

对此的响应会是： 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
              "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
              "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
              ]
        }"
    }

### <a name="detectors"></a>检测程序
异常情况检测 API 支持检测程序中的 3 个主要类别。 可在下表中找到特定输入的参数和每个检测程序输出结果的详细信息。

| 检测程序类别 | 检测程序 | 说明 | 输入参数 | Outputs |
| --- | --- | --- | --- | --- |
| 峰值检测程序 |TSpike 检测程序 |根据值与第一个和第三个四分位数的距离，检测峰值和低值 |*tspikedetector.sensitivity：*采用 1-10 中的整数值、默认值：3；较高的值会捕获多个极值，从而使敏感度降低 |TSpike：二进制值 – 如果检测到峰值或低值，显示“1”，否则为“0” |
| ZSpike 检测程序 |根据数据点与平均值的距离，检测峰值和低值 |*tspikedetector.sensitivity：*采用 1-10 范围内的整数值，默认值为 3；值越大，捕获的值更极端，使敏感程度降低 |TSpike：二进制值 – 如果检测到峰值或低值，显示“1”，否则为“0” | |
| 慢速趋势检测程序 |慢速趋势检测程序 |根据每组的敏感度，检测慢速正面发展趋势 |*trenddetector.sensitivity：*检测程序分数上的阈值（默认值：3.25，3.25 – 5 是合理的选择范围；值越高敏感性越低） |TScore：浮动数字表示趋势的异常分数 |
| 级别更改检测程序 |单向级别更改检测程序 |根据每组的敏感度，检测向上级别的更改 |*upleveldetector.sensitivity：*检测程序分数上的阈值（默认值：3.25，3.25 – 5 是合理的选择范围；值越高敏感性越低） |PScore：浮动数字表示向上级别更改上的异常分数 |
| 双向级别更改检测程序 |根据每组的敏感度，检测向下和向上级别的更改 |*bileveldetector.sensitivity：*检测程序分数上的阈值（默认值：3.25，3.25 – 5 是合理的选择范围；值越高敏感性越低） |RPScore：浮动数字代表向上和向下级别更改的异常分数 | |

### <a name="parameters"></a>parameters
下表中列出了这些输入参数的更多详细信息：

| 输入参数 | 说明 | 默认设置 | 类型 | 有效的范围 | 推荐的区域 |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |聚合输入时间序列的聚合间隔单位为秒 |0（不执行任何聚合） |integer |0：跳过聚合，> 0 否则 |5 分钟到 1 天，时间系列依赖项 |
| preprocess.aggregationFunc |用于将数据聚合到指定的 AggregationInterval 函数 |平均值 |枚举 |平均值、总和、长度 |不适用 |
| preprocess.replaceMissing |用户输入缺失的数据值 |lkv（最近一次的值） |枚举 |零、lkv、平均值 |不适用 |
| detectors.historyWindow |用于记录异常分数计算结果（在数据点的 #） |500 |integer |10-2000 |时间序列依赖项 |
| upleveldetector.sensitivity |向上级别更改检测程序的敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| bileveldetector.sensitivity |双向级别更改检测程序的敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| 趋势检测程序敏感度 |正面发展趋势检测程序敏感度。 |3.25 |double |无 |3.25-5（值越小，敏感度越高） |
| tspikedetector.sensitivity |TSpike 检测程序的敏感度 |3 |integer |1-10 |3 5（值越小，敏感度越高） |
| zspikedetector.sensitivity |ZSpike 检测程序的敏感度 |3 |integer |1-10 |3 5（值越小，敏感度越高） |
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
| Pscore |浮动数字表示向上级别更改上的异常分数 |
| Palert |1/0 值指示基于输入敏感度的、上升级别的更改异常 |
| RPScore |浮动数字表示双向级别更改上的异常分数 |
| RPAlert |基于输入敏感度，指示存在双向级别更改异常的 1/0 值 |
| TScore |浮动数字表示正面趋势的更改异常 |
| TAlert |1/0 值指示存在基于输入敏感度的、正面趋势异常 |

可以使用[简单分析器](https://adresultparser.codeplex.com/)分析输出 - 它具有示例代码，演示如何连接到 API 并分析输出。 可在仪表板上可视化检测到的异常，和/或传递给工作的专家纠正或集成到订票系统。

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png







<!--HONumber=Nov16_HO3-->


