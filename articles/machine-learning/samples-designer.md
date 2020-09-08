---
title: 设计器的示例管道和数据集（预览版）
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器中的示例来快速开始生成机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.custom: designer
ms.openlocfilehash: c4fa5a2554f10a720c0f2241e17514e798cf7f4c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88812931"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer-preview"></a>Azure 机器学习设计器的示例管道和数据集（预览版）

使用 Azure 机器学习设计器中的内置示例，快速开始生成自己的机器学习管道。 Azure 机器学习设计器 [GitHub 存储库](https://github.com/Azure/MachineLearningDesigner)包含了可帮助你了解某些常见机器学习方案的详细文档。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://aka.ms/AMLFree)。
* 使用企业 SKU 的 Azure 机器学习工作区。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>使用示例管道

设计器会将示例管道的副本保存到工作室工作区。 你可以编辑管道，以根据自己的需求对其进行改编；还可以保存管道供自己使用。 可以使用这些示例作为起点来快速开始生成项目。

以下介绍如何使用设计器示例：

1. 登录到 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，选择要使用的工作区。

1. 选择“设计器”。

1. 在“新建管道”部分下选择一个示例管道。

    选择“显示更多示例”查看完整的示例列表。

1. 若要运行管道，必须首先设置要在其上运行管道的默认计算目标。

   1. 在画布右侧的“设置”窗格中，选择“选择计算目标”。 

   1. 在出现的对话框中，选择现有的计算目标或创建新的计算目标。 选择“保存”。

   1. 选择画布顶部的“提交”，提交管道运行。

   运行的完成可能需要一些时间，具体取决于示例管道和计算设置。 默认计算设置中的最小节点大小为 0，这意味着设计器必须在空闲后分配资源。 由于计算资源已分配，因此，重复的管道运行花费的时间会更少。 此外，设计器还对每个模块使用缓存的结果，以便进一步提高效率。


1. 管道运行完毕后，可以查看管道，还可以查看每个模块的输出，了解详细信息。 使用以下步骤查看模块输出：

   1. 右键单击画布中要查看其输出的模块。
   1. 选择“可视化”。


   从示例着手，了解一些最常见的机器学习方案。

## <a name="regression"></a>回归

探索这些内置的回归示例。

| 标题示例 | 说明 | 
| --- | --- |
| [示例 1：回归 - 汽车价格预测(基本)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md)。 | 使用线性回归预测汽车价格。 |
| [示例 2：回归 - 汽车价格预测(高级)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md)。 | 使用决策林和提升决策树回归器预测汽车价格。 比较模型以找出最佳算法。

## <a name="classification"></a>分类

探索这些内置的分类示例。 对于未提供文档链接的示例，可将其打开并查看模块注释来详细了解这些示例。

| 标题示例 | 说明 | 
| --- | --- |
| [示例 3：通过特征选择进行二元分类 - 收入预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | 使用双类提升决策树预测收入的高低。 使用皮尔逊相关选择特征。
| [示例 4：通过自定义 Python 脚本进行二元分类 - 信用风险预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | 将信贷申请分类为高风险或低风险。 使用“执行 Python 脚本”模块为数据加权。
| [示例 5：二元分类 - 客户关系预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | 使用双类提升决策树预测客户流失率。 使用 SMOTE 对有偏差的数据采样。
| [示例 7：文本分类 - 维基百科 SP 500 数据集](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | 使用多类逻辑回归对维基百科文章中的公司类型进行分类。 |
| 示例 12：多类分类 - 字母识别 | 创建二元分类器的系综，对手写字母进行分类。 |

## <a name="recommender"></a>推荐器

探索这些内置的推荐器示例。 对于未提供文档链接的示例，可将其打开并查看模块注释来详细了解这些示例。

| 标题示例 | 说明 | 
| --- | --- |
| 示例 10：推荐 - 电影评级推文 | 基于电影标题和评级生成电影推荐器引擎。 |

## <a name="utility"></a>实用工具

详细了解用于演示机器学习实用工具和功能的示例。 对于未提供文档链接的示例，可将其打开并查看模块注释来详细了解这些示例。

| 标题示例 | 说明 | 
| --- | --- |
| [示例 6：使用自定义 R 脚本 - 航班延误预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| 示例 8：二元分类的交叉验证 - 成人收入预测 | 使用交叉验证生成用于预测成人收入的二元分类器。
| 示例 9：排列特征重要性 | 使用排列特征重要性来计算测试数据集的重要性评分。 
| 示例 11：优化二元分类的参数 - 成人收入预测 | 使用“优化模型超参数”找出用于生成二元分类器的最佳超参数。 |

## <a name="datasets"></a>数据集

在 Azure 机器学习设计器中创建新管道时，其中会默认包含多个示例数据集。 设计器主页中的示例管道使用这些示例数据集。 

示例数据集在“数据集-示例”类别下提供。  可以在设计器的画布左侧的模块面板中找到它。 将其中的任何数据集拖放到画布中即可在自己的管道中使用它们。

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


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

若要了解有关预测分析和机器学习的基本知识，请参阅[教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)

