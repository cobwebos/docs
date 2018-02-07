---
title: "在机器学习工作室中使用示例数据集 | Microsoft Docs"
description: "机器学习工作室中包含的示例模型中使用的数据集说明。 可以将这些示例数据集用于实验。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: garye
ms.openlocfilehash: 3339d3b3bab2c0ce210950c178ef03fc6c0ecab9
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2018
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中使用示例数据集
[top]: #machine-learning-sample-datasets

在 Azure 机器学习中创建新工作区时，默认包含大量的示例数据集和实验。 其中许多示例数据集由 [Azure AI 库](http://gallery.cortanaintelligence.com/)中的示例模型使用。 其他示例数据集作为通常在机器学习中使用的各种类型数据的示例包含在内。

一些数据集可在 Azure Blob 存储中使用。 对于这些数据集，下表提供了直接链接。 可以在实验中通过使用[导入数据][import-data]模块使用这些数据集。

余下的这些示例数据集可在“保存的数据集”下的工作区中使用。 可以在机器学习工作室中试验画布左侧的模块调色板中找到此项。
通过将其中的任何数据集拖放到实验画布中，即可在自己的实验中使用它们。


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

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
<b>相关研究：</b>Kohavi, R.、Becker, B.（1996 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>机场代码数据集</td>
  <td>
美国机场代码。
<p></p>
此数据集中的一行包含每一个美国机场，其中提供机场 ID 号和名称，以及所在位置的城市和州。
  </td>
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
<b>相关研究：</b>Schlimmer, J.C. （1987 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>自行车租赁 UCI 数据集</td>
  <td>
UCI 自行车租赁数据集基于 Capital Bikeshare 公司的实际数据，该公司维护着美国华盛顿特区的自行车租赁网络。
<p></p>
该数据集中的一行对应于 2011 年和 2012 年中每一天的每个小时，总共 17,379 行。 每小时自行车租车数范围从 1 到 977。

  </td>
</tr>

<tr>
  <td>Bill Gates RGB 图像</td>
  <td>
转换为 CSV 数据的公开可用的映像文件。
<p></p>
在<strong>使用 K 平均值聚类的颜色量化</strong>模型详细信息页中提供了用于转换该图像的代码。
  </td>
</tr>

<tr>
  <td>献血数据</td>
  <td>
来自台湾新竹市输血服务中心献血数据库的数据子集。
<p></p>
捐献者数据包括自上次捐献的时隔月数和频率，或捐献总数、自上次捐献的时隔时间和献血量。
<p></p>
<b>用途：</b>目标是通过分类预测 2007 年 3 月是否会有献血者，其中 1 表示目标期间有献血者，0 表示没有献血者。 
<p></p>
<b>相关研究：</b>Yeh, I.C.（2008 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院
<p></p>
Yeh, I-Cheng, Yang, King-Jang, 和 Ting, Tao-Ming, “Knowledge discovery on RFM model using Bernoulli sequence”, 专家系统及其应用, 2008 年, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>乳腺癌数据</td>
  <td>
肿瘤学研究所提供的三个癌症相关数据集之一，经常出现在机器学习文献中。 将诊断信息与来自约 300 个组织样本的实验室分析的特征相结合。
<p></p>
<b>用途：</b>基于 9 种属性（其中有一些是线性的，一些是无条件的）分类癌症类型。 
<p></p>
<b>相关研究：</b>Wohlberg, W.H.、Street, W.N. 和 Mangasarian, O.L. （1995 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>乳腺癌特征 <td>
数据集包含 X 射线图像的 102K 个可疑区域（候选）的信息，每个由 117 个特征描述。 这些特征是专有的，数据集创建者（即 Siemens Healthcare）不会透露其含义。 
  </td>
</tr>

<tr>
  <td>乳腺癌信息</td>
  <td>
数据集包含 X 射线图像的每个可疑区域的附加信息。 每个示例提供关于乳腺癌特征数据集中的相应行数的信息（例如，标签、患者 ID、相对于整个图像的贴片坐标）。 每位病人有许多示例。 对于患有癌症的患者，一些示例是积极的，一些示例是消极的。 对于未患癌症的患者，所有示例都是消极的。 数据集有 102K 个示例。 数据集有偏差，0.6％ 的点为正，其余为负。 数据集由 Siemens Healthcare 提供。
  </td>
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
此数据来自 KDD Cup 2009 客户关系预测挑战赛 (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>)。
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
  <td>能效回归数据</td>
  <td>
模拟能量分布的集合，基于 12 种不同的建筑形状。 建筑通过 8 个特征区分。 这包括玻璃窗面积、玻璃窗面积分布和方向。
<p></p>
<b>用途：</b>使用回归或分类来预测基于两个实值响应之一的能效等级。 对于多类分类，将响应变量舍入为最接近的整数。 
<p></p>
<b>相关研究：</b>Xifara, A. 和 Tsanas, A.（2012 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>航班延误数据</td>
  <td>
从美国的 TranStats 数据收集中获得的客运航班正常率数据。交通部（<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">准时</a>）。
<p></p>
数据集涵盖 2013 年 4 月到 10 月的时间段。 在上传到 Azure 机器学习工作室之前，数据集的处理如下所示：
<ul>
  <li>数据集经筛选，仅包含美国本土 70 个最繁忙的机场</li>
  <li>取消的航班标记为延误超过 15 分钟</li>
  <li>转机航班已筛选掉</li>
  <li>已选择以下各列：Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>航班正常率（原始）</td>
  <td>
美国 2011 年 10 月航班到达和出发的记录。
<p></p>
<b>用途：</b>预测航班延误。 
<p></p>
<b>相关研究：</b>美国交通部网址：<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>。
  </td>
</tr>

<tr>
  <td>森林火灾数据</td>
  <td>
包含天气数据，如温度和湿度指数和风速。 该数据取自葡萄牙东北部地区，并包含森林火灾记录。
<p></p>
<b>用途：</b>这项回归任务非常难，其目的是预测森林火灾的焚毁面积。 
<p></p>
<b>相关研究：</b>Cortez, P. 和 Morais, A.（2008 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院
<p></p>
[Cortez 和 Morais，2007 年] P. Cortez 和 A. Morais。 使用气象数据预测森林火灾的数据挖掘方法。 在 J. Neves, M. F. Santos 和 J. Machado Eds., 人工智能的新趋势, 2007 第 13 届 EPIA 的会议记录 - 有关人工智能的葡萄牙语大会, 12 月, Guimarães, 葡萄牙, pp. 512-523, 2007 年。 APPIA, ISBN-13 978-989-95618-0-9。 获取网址：<a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>。
  </td>
</tr>

<tr>
  <td>德国信用卡 UCI 数据集</td>
  <td>
使用 german.data 文件的 UCI Statlog（德国信用卡）数据集 (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>)。
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
  <td>鸢尾花双类数据</td>
  <td>
这可能是模式识别文献中最有名的数据库。 数据集相对较小，三种鸢尾花品种的每个花瓣测量均包含 50 个示例。
<p></p>
<b>用途：</b>通过测量值预测鸢尾花类型。  
<p></p>
<b>相关研究：</b>Fisher, R.A. （1988 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>电影推文</td>
  <td>
该数据集是电影迷你推文数据集的扩展版本。 数据集中具有 170K 个电影评分，从 Twitter 上结构良好的推文中提取。 每个实例标识一篇推文和如下元组：用户 ID、IMDB 电影 ID、评分、时间戳、收藏此推文的数目和转发此推文的数目。 数据集由 A. Said、S. Dooms、B. Loni 和 D. Tikk 提供，用于 Recommender Systems Challenge 2014。
  </td>
</tr>

<tr>
  <td>各种汽车的 MPG 数据</td>
  <td>
此数据集是美国卡内基梅隆大学的 StatLib 库提供的数据集略有修改的版本。 1983 年美国统计协会博览会使用过该数据集。
<p></p>
该数据列出各种汽车的油耗（以每加仑英里数为计量单位）。 还包括汽缸数、发动机排量、马力、总重量和加速性能等信息。
<p></p>
<b>用途：</b>基于 3 个多值离散属性和 5 个连续属性预测燃料经济性。 
<p></p>
<b>相关研究：</b>StatLib，美国卡内基梅隆大学（1993 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>皮马族印地安人糖尿病二元分类数据集</td>
  <td>
美国国家糖尿病、消化和肾脏疾病研究所数据库数据的一个子集。 数据集经筛选，重点研究皮马族印第安人后代的女性患者。 数据包括葡萄糖和胰岛素水平等医疗数据，以及生活方式因素。
<p></p>
<b>用途：</b>预测观察对象是否患有糖尿病（二元分类）。 
<p></p>
<b>相关研究：</b>Sigillito, V.（1990 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml"</a>。 加州大学欧文分校的信息与计算机科学学院 </td>
</tr>

<tr>
  <td>餐馆客户数据</td>
  <td>
一组关于客户的元数据，其中包括人口统计信息和偏好。
<p></p>
<b>用途：</b>将此数据集与其他两个餐馆数据集结合使用，以便训练和测试推荐器系统。 
<p></p>
<b>相关研究：</b>Bache, K. 和 Lichman, M.（2013 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院。
  </td>
</tr>

<tr>
  <td>餐馆特色数据</td>
  <td>
一组关于餐馆及其特色的元数据，如食物种类、就餐样式和位置。
<p></p>
<b>用途：</b>将此数据集与其他两个餐馆数据集结合使用，以便训练和测试推荐器系统。 
<p></p>
<b>相关研究：</b>Bache, K. 和 Lichman, M.（2013 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院。
  </td>
</tr>

<tr>
  <td>餐馆评分</td>
  <td>
包含用户对餐馆的评分，分数范围从 0 到 2。
<p></p>
<b>用途：</b>将此数据集与其他两个餐馆数据集结合使用，以便训练和测试推荐器系统。 
<p></p>
<b>相关研究：</b>Bache, K. 和 Lichman, M.（2013 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院。
  </td>
</tr>

<tr>
  <td>钢退火多类数据集</td>
  <td>
该数据集包含钢退火实验的一系列记录。 它包含生成钢种的物理属性（宽度、厚度、类型（卷材、板材等））。
<p></p>
<b>用途：</b>预测两个数值类属性的任何一个：硬度或强度。 还可以分析这些属性之间的相关性。
<p></p>
钢种遵循由 SAE 和其他组织定义的一组标准。 正在寻找特定“种类”（类变量），并想要了解所需值。 
<p></p>
<b>相关研究：</b>Sterling, D. 和 Buntine, W.(NA)。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息与计算机科学学院
<p></p>
可以在以下位置找到钢种的有用指南：<a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>望远镜数据</td>
  <td>
高能量伽玛粒子爆发以及背景噪声的记录，两者都使用蒙特卡洛方法模拟。
<p></p>
该模拟的目的是提高路基 Cherenkov 大气伽玛望远镜的准确性。 这通过使用统计方法来区分所需信号（Cherenkov 辐射淋浴）和背景噪声（由上层大气中的宇宙射线引发的强子淋浴）实现。
<p></p>
已预处理数据，创建细长的群集，其中长轴朝向相机中心。 该椭圆的特征（通常称为 Hillas 参数）是可以用于辨别的图像参数。
<p></p>
<b>用途：</b>预测淋浴图像表示信号，还是表示背景噪声。
<p></p>
<b>说明：</b>简单的分类精度对于该数据没有意义，因为与将信号事件分类为背景相比，更糟糕的是将背景事件分类为信号。 为了比较不同的分类器，应当使用 ROC 图。 接受背景事件作为信号的概率必须低于以下阈值之一：0.01、0.02、0.05、0.1 或 0.2。
<p></p>
另请注意，背景事件的数目（h 表示强子淋浴）被低估。 在实际测量中，h 或噪声类代表大部分事件。 
<p></p>
<b>相关研究：</b>Bock, R.K. （1995 年）。 UCI 机器学习存储库网址：<a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>。 加州大学欧文分校的信息学院 </td>
</tr>

<tr>
  <td>天气数据集</td>
  <td>
美国国家海洋和大气局每小时发布的陆基天气观测（<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">从 201304 到 201310 的合并数据</a>）。
<p></p>
该天气数据包括从机场气象站获取的观测结果，涵盖的时间段为 2013 年 4 月到 10 月。 在上传到 Azure 机器学习工作室之前，数据集的处理如下所示：
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
数据来自维基百科 (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>)，基于每个标准普尔 500 强公司的文章，存储为 XML 数据。
<p></p>
在上传到 Azure 机器学习工作室之前，数据集的处理如下所示：
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

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
数据集包含客户数据和有关其响应直接邮寄活动的迹象。 每一行表示一位客户。 数据集包含 9 个关于用户统计信息和过往行为的特征，以及 3 个标签列（访问、转化和支出）。  访问是一个二进制列，指示客户是在市场营销活动后访问的。 转化指示客户购买了物品。 支出为购物金额。  数据集由 Kevin Hillstrom 提供，用于 MineThatData E-Mail Analytics And Data Mining Challenge。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
RCV1-V2 路透社新闻数据集中测试示例的特征。 该数据集中具有 781K 个新闻文章及其 ID（数据集的第一列）。 每篇文章已经过标记化、设置了停用词和去除枝枝叶叶。 数据集由 David 提供。 D. Lewis。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
RCV1-V2 路透社新闻数据集中训练示例的特征。 该数据集中具有 23K 个新闻文章及其 ID（数据集的第一列）。 每篇文章已经过标记化、设置了停用词并去除了枝枝叶叶。 数据集由 David 提供。 D. Lewis。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
来自 KDD Cup 1999 知识发现和数据挖掘工具竞赛 (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>) 的数据集。
<p></p>
该数据集已下载并存储在 Azure Blob 存储 (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) 中，包含训练和测试数据集。 训练数据集大约有 12.6 万行和 43 列，包括标签。 3 列是标签信息的组成部分，40 列由数字和字符串/分类特征组成，可用于训练模型。 测试数据具有大约 22.5K 个测试示例，具有与训练数据相同的 43 列。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
RCV1-V2 路透社新闻数据集中新闻文章的主题分配。 可以分配给新闻文章多个主题。 每行的格式为“&lt;主题名称&gt; &lt;文档 ID&gt; 1”。 数据集包含 2.6M 个主题分配。 数据集由 David 提供。 D. Lewis。
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
此数据来自 KDD Cup 2010 学生成绩评估挑战赛（<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">学生成绩评估</a>）。 使用的数据为 Algebra_2008_2009 训练集 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. （2010 年）。 代数 I 2008-2009。 来自 KDD Cup 2010 教育数据挖掘挑战赛的挑战数据集。 可在 <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> 或 <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a> 中找到该数据。
<p></p>
该数据集已下载并存储在 Azure Blob 存储 (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) 中，包含学生辅导系统中的日志文件。 提供的特征包含问题 ID 及其简要描述、学生 ID、时间戳以及学生在正确解决该问题之前尝试的次数。 原始数据集具有 890 万条记录，此数据集已降低取样为前 10 万行。 该数据集具有 23 个制表符分隔的各种类型的列：数值、分类和时间戳。
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
