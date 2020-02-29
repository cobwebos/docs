---
title: 使用示例数据集
titleSuffix: Azure Machine Learning
description: 机器学习设计器中包含的示例模型中使用的数据集的说明。 您可以将这些示例数据集用于管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165057"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>在 Azure 机器学习设计器中使用示例数据集（预览）

如果在 Azure 机器学习设计器（预览版）中创建新管道，则默认情况下将包含多个示例数据集。 其中许多示例数据集由设计器主页中的示例模型使用。 其他示例数据集作为通常在机器学习中使用的各种类型数据的示例包含在内。

一些数据集可在 Azure Blob 存储中使用。 对于这些数据集，下表提供了直接链接。 您可以使用 "[导入数据](./algorithm-module-reference/import-data.md)" 模块在管道中使用这些数据集。

这些示例数据集的其余部分在 "**数据集**-**示例**" 类别下提供。 可以在设计器中画布左侧的模块面板中找到此项。 您可以通过将其中的任何数据集拖到画布来在自己的管道中使用这些数据集。

## <a name="datasets"></a>数据集

<table>

<tr>
  <th>数据集名称</th>
  <th>数据集说明</th>
</tr>

<tr>
  <td>成年人口收入二元分类数据集</td>
  <td>
1994 年人口普查数据库的子集（其中在职人士年龄大于 16，调整后的收入指数大于 100）。
<p></p>
<b>用途</b>：使用人口统计信息对国民进行分类，预测是否有国民的年收入超过 50K。
<p></p>
<b>相关研究：</b>Kohavi, R.、Becker, B.（1996 年）。 UCI 机器学习存储库 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>汽车价格数据（原始）</td>
  <td>
有关汽车品牌和型号的信息，包括价格、汽缸数和 MPG 等特性以及保险风险评分。
<p></p>
风险评分最初与自动定价关联。 然后，针对精算师所熟知符号化过程中的实际风险进行调整。 值为 +3 表明汽车存在风险，值为 -3 表明汽车可能安全。
<p></p>
<b>用途：</b>按特性、使用回归或多元分类预测风险评分。 
<p></p>
<b>相关研究：</b>Schlimmer, J.C. （1987 年）。 UCI 机器学习存储库 <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>


<tr>
  <td>共享的 CRM 亲和力标签</td>
  <td>
来自 KDD Cup 2009 客户关系预测挑战赛的标签 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>)。
  </td>
</tr>

<tr>
  <td>共享的 CRM 流失情况标签</td>
  <td>
来自 KDD Cup 2009 客户关系预测挑战赛的标签 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>)。
  </td>
</tr>

<tr>
  <td>共享的 CRM 数据集</td>
  <td>
此数据来自 KDD Cup 2009 客户关系预测挑战赛 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>)。
<p></p>
数据集包含法国电信公司 Orange 的 50K 个客户。 每个客户都有 230 个匿名特征，其中 190 个是数字的，其余 40 个是分类的。 特征非常稀疏。
  </td>
</tr>

<tr>
  <td>共享的 CRM 追加销售标签</td>
  <td>
来自 KDD Cup 2009 客户关系预测挑战赛的标签 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>)。
  </td>
</tr>

<tr>
  <td>航班延误数据</td>
  <td>
乘客航班时间从美国交通部的 TranStats 数据收集获取的时间性能数据（<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">按时）。</a>
<p></p>
数据集涵盖 2013 年 4 月到 10 月的时间段。 在上传到设计器之前，数据集的处理方式如下：
<ul>
  <li>数据集经筛选，仅包含美国本土 70 个最繁忙的机场</li>
  <li>取消的航班标记为延误超过 15 分钟</li>
  <li>转机航班已筛选掉</li>
  <li>已选择以下各列：Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>德国信用卡 UCI 数据集</td>
  <td>
使用 german.data 文件的 UCI Statlog（德国信用卡）数据集 (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>)。
<p></p>
数据集将用户（由一组属性描述）分为两类：低信用风险或高信用风险。 每个示例表示一位用户。 有 20 个特征，包括数值和分类，以及二进制标签（信用风险值）。 高信用风险条目具有标签 = 2，低信用风险条目具有标签 = 1。 将低风险示例错误分类为高的成本是 1，反之将高风险示例错误分类为低的成本是 5。
  </td>
</tr>

<tr>
  <td>IMDB 电影标题</td>
  <td>
数据集包含 Twitter 推文中给电影评分的有关信息：IMDB 电影 ID、电影名称、风格和制作年份。 数据集中有 17K 个电影。 报告“S. Dooms、T. De Pessemier 和 L. Martens. MovieTweetings：从 Twitter 收集的电影评分数据集。 有关适用于推荐器系统 (CrowdRec at RecSys 2013) 的众包和人工计算研讨会。”中引用了该数据集。
  </td>
</tr>

<tr>
  <td>电影分级</td>
  <td>
该数据集是电影迷你推文数据集的扩展版本。 数据集中具有 170K 个电影评分，从 Twitter 上结构良好的推文中提取。 每个实例表示一个推文，是一个元组：用户 ID、IMDB 电影 ID、分级、时间戳、此推文的收藏夹数，以及此推文的篇以上数。 数据集由 A. Said、S. Dooms、B. Loni 和 D. Tikk 提供，用于 Recommender Systems Challenge 2014。
  </td>
</tr>


<tr>
  <td>天气数据集</td>
  <td>
美国国家海洋和大气局每小时发布的陆基天气观测（<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">从 201304 到 201310 的合并数据</a>）。
<p></p>
该天气数据包括从机场气象站获取的观测结果，涵盖的时间段为 2013 年 4 月到 10 月。 在上传到设计器之前，数据集的处理方式如下：
<ul>
  <li>气象站 ID 已映射到相应的机场 ID</li>
  <li>与 70 个最繁忙的机场无关的气象站已筛选掉</li>
  <li>Date 列已拆分为单独的 Year、Month 和 Day 列</li>
  <li>已选择以下各列：AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>维基百科 SP 500 数据集</td>
  <td>
数据来自维基百科 (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>)，基于每个标准普尔 500 强公司的文章，存储为 XML 数据。
<p></p>
在上传到设计器之前，数据集的处理方式如下：
<ul>
  <li>提取每个特定公司的文本内容</li>
  <li>去除维基百科的格式设置</li>
  <li>去除非字母数字字符</li>
  <li>将所有文本都转换为小写</li>
  <li>添加了已知的公司类别</li>
</ul>
<p></p>
请注意，可能找不到某些公司的文章，因此记录数小于 500。
  </td>
</tr>

</table>

## <a name="next-steps"></a>后续步骤

* 了解有关预测分析和机器学习的基本知识[教程：通过设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)

* 使用 "[导入数据](./algorithm-module-reference/import-data.md)" 模块导入示例数据集
