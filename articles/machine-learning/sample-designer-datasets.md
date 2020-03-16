---
title: 在 Azure 机器学习设计器中使用示例数据集
titleSuffix: Azure Machine Learning
description: 详细了解 Azure 机器学习设计器中包含的示例数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037303"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>在 Azure 机器学习设计器（预览版）中使用示例数据集

在 Azure 机器学习设计器（预览版）中创建新管道时，其中会默认包含多个示例数据集。 设计器主页中的示例管道使用这些示例数据集。 

示例数据集在“数据集-示例”类别下提供。   可以在设计器的画布左侧的模块面板中找到它。 将其中的任何数据集拖放到画布中即可在自己的管道中使用它们。

## <a name="datasets"></a>数据集


| 数据集名称&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| 数据集说明 |
|-------------|:--------------------|
| 成年人口收入二元分类数据集 | 1994 年人口普查数据库的子集（其中在职人士年龄大于 16，调整后的收入指数大于 100）。<br/>**使用情况**：使用人口统计信息对人员分类，预测某人的年收入是否超过 5 万。<br/> **相关研究**：Kohavi, R.、Becker, B.（1996 年）。 [UCI 机器学习存储库](https://archive.ics.uci.edu/ml)。 加州大学欧文分校的信息与计算机科学学院|
|汽车价格数据（原始）|有关汽车品牌和型号的信息，包括价格、汽缸数和 MPG 等特性以及保险风险评分。<br/> 风险评分最初与自动定价关联。 然后，针对精算师所熟知符号化过程中的实际风险进行调整。 值为 +3 表明汽车存在风险，值为 -3 表明汽车可能安全。<br/>**用法**：</b>按特性、使用回归或多元分类预测风险评分。<br/>**相关研究**：</b>Schlimmer, J.C. （1987 年）。 [UCI 机器学习存储库](https://archive.ics.uci.edu/ml)。 加州大学欧文分校的信息与计算机科学学院。 |
| 共享的 CRM 亲和力标签 |来自 KDD Cup 2009 客户关系预测挑战赛的标签 ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels))。|
|共享的 CRM 流失情况标签|来自 KDD Cup 2009 客户关系预测挑战赛的标签 ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels))。|
|共享的 CRM 数据集 | 此数据来自 KDD Cup 2009 客户关系预测挑战赛 ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip))。 <br/>数据集包含法国电信公司 Orange 的 50K 个客户。 每个客户都有 230 个匿名特征，其中 190 个是数字的，其余 40 个是分类的。 特征非常稀疏。 |
|共享的 CRM 追加销售标签|来自 KDD Cup 2009 客户关系预测挑战赛的标签 ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|航班延误数据|从美国的 TranStats 数据收集中获得的客运航班正常率数据。交通部（[准时](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)）。<br/>数据集涵盖 2013 年 4 月到 10 月的时间段。 在上传到设计器之前，数据集的处理如下所述： <br/>- 数据集经筛选，仅涵盖美国本土 70 个最繁忙的机场 <br/>- 取消的航班标记为延误超过 15 分钟 <br/>- 转机航班已筛选掉 <br/>- 已选择以下各列：Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Canceled|
|德国信用卡 UCI 数据集|使用 german.data 文件的 UCI Statlog（德国信用卡）数据集 ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)))。<br/>数据集将用户（由一组属性描述）分为两类：低信用风险或高信用风险。 每个示例表示一位用户。 有 20 个特征，包括数值和分类，以及二进制标签（信用风险值）。 高信用风险条目具有标签 = 2，低信用风险条目具有标签 = 1。 将低风险示例错误分类为高的成本是 1，反之将高风险示例错误分类为低的成本是 5。|
|IMDB 电影标题|数据集包含 Twitter 推文中给电影评分的有关信息：IMDB 电影 ID、电影名称、风格和制作年份。 数据集中有 17K 个电影。 报告“S. Dooms、T. De Pessemier 和 L. Martens. MovieTweetings：从 Twitter 收集的电影评分数据集。 有关适用于推荐器系统 (CrowdRec at RecSys 2013) 的众包和人工计算研讨会。”中引用了该数据集。|
|电影评分|该数据集是电影迷你推文数据集的扩展版本。 数据集中具有 170K 个电影评分，从 Twitter 上结构良好的推文中提取。 每个实例表示一篇推文，是一个元组：用户 ID、IMDB 电影 ID、评分、时间戳、收藏此推文的数目和转发此推文的数目。 数据集由 A. Said、S. Dooms、B. Loni 和 D. Tikk 提供，用于 Recommender Systems Challenge 2014。|
|天气数据集|美国国家海洋和大气局每小时发布的陆基天气观测（[从 201304 到 201310 的合并数据](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)）。<br/>该天气数据包括从机场气象站获取的观测结果，涵盖的时间段为 2013 年 4 月到 10 月。 在上传到设计器之前，数据集的处理如下所述：    <br/> -    气象站 ID 已映射到相应的机场 ID    <br/> -    与 70 个最繁忙的机场无关的气象站已筛选掉    <br/> -    Date 列已拆分为单独的 Year、Month 和 Day 列    <br/> - 已选择以下各列：AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter|
|维基百科 SP 500 数据集|数据来自维基百科 (https://www.wikipedia.org/) ，基于每个标准普尔 500 强公司的文章，存储为 XML 数据。    <br/>在上传到设计器之前，数据集的处理如下所述：    <br/> - 提取每个具体公司的文本内容    <br/> -    去除 wiki 格式设置    <br/> -    去除非字母数字字符    <br/> -    将所有文本都转换为小写    <br/> -    添加了已知的公司类别    <br/>请注意，可能找不到某些公司的文章，因此记录数小于 500。|

## <a name="next-steps"></a>后续步骤

* 若要了解有关预测分析和机器学习的基本知识，请参阅[教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)

* 了解如何根据自己的需求修改现有的[设计器示例](samples-designer.md)。
