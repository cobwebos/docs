---
title: 解释模型结果
titleSuffix: ML Studio (classic) - Azure
description: 如何为使用并可视化评分模型输出的算法选择最佳的参数集。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: 23a201ef63a9a1a6bb5bd078867ccc1200893049
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982014"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>在 Azure 机器学习工作室（经典版）中解释模型结果

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
本主题说明如何在 Azure 机器学习工作室（经典版）中可视化和解释预测结果。 训练模型并根据它进行预测（“为模型评分”）后，需要了解并解释预测结果。

Azure 机器学习工作室（经典版）中有四种主要的机器学习模型：

* 分类
* 群集功能
* 回归
* 推荐器系统

用于基于这些模型进行预测的模块包括：

* 用于分类和回归的[评分模型][score-model]模块
* 用于聚类分析的[分配到群集][assign-to-clusters]模块
* 用于推荐系统的 [Matchbox 推荐器评分][score-matchbox-recommender]

了解如何[选择参数以优化 ML Studio （经典）中的算法](algorithm-parameters-optimize.md)。

若要了解如何评估模型，请参阅[如何评估模型性能](evaluate-model-performance.md)。

如果你不熟悉 ML Studio （经典），请[学习如何创建一个简单的实验](create-experiment.md)。

## <a name="classification"></a>分类
分类问题有两个子类别：

* 仅两个类的问题（双类或二元分类）
* 多于两个类的问题（多类分类）

Azure 机器学习工作室（经典版）具有不同的模块用于处理其中每一种分类，但用于解释其预测结果的方法都相似。

### <a name="two-class-classification"></a>双类分类
**示例实验**

双类分类问题的一个示例是鸢尾花的分类。 任务是根据特征为鸢尾花分类。 Azure 机器学习工作室（经典版）中提供的鸢尾花数据集是流行的[鸢尾花数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)的子集，仅包含两种花卉种类（类 0 和类 1）的实例。 每个花卉有四个特征（花萼长度、花萼宽度、花瓣长度和花瓣宽度）。

![鸢尾花实验的屏幕截图](./media/interpret-model-results/1.png)

图 1. 鸢尾花双类分类问题实验

已执行实验来解决此问题，如图 1 所示。 已训练并评分双类提升决策树模型。 现在，可以可视化[评分模型][score-model]模块的预测结果，方法是单击[评分模型][score-model]模块的输出端口，并单击“可视化”****。

![评分模型模块](./media/interpret-model-results/1_1.png)

这会打开评分结果，如图 2 所示。

![鸢尾花双类分类实验的结果](./media/interpret-model-results/2.png)

图 2。 在双类分类中可视化评分模型结果

**结果解释**

结果表中有六列。 左边的四列是四个特征。 右边的两列“评分标签”和“评分概率”是预测结果。 “评分概率”列显示花卉属于正类（类 1）的概率。 例如，列中的第一个数字 (0.028571) 表示第一个花卉属于类 1 的概率为 0.028571。 “评分标签”列显示每个花卉的预测类。 这基于“评分概率”列。 如果花卉的评分概率大于 0.5，则它预测为类 1。 否则，它预测为类 0。

**Web 服务发布**

在了解了预测结果并判断其为优良后，可以将实验发布为 Web 服务，以便可以将其部署在各种应用程序中，并对任何新的鸢尾花调用它以获取类预测。 若要了解如何将训练实验更改为评分实验并将其发布为 web 服务，请参阅[教程3：部署信用风险模型](tutorial-part3-credit-risk-deploy.md)。 此过程提供评分实验，如图 3 所示。

![评分实验的屏幕截图](./media/interpret-model-results/3.png)

图 3。 为鸢尾花双类分类问题实验评分

现在需要设置 Web 服务的输入和输出。 输入是[评分模型][score-model]的右输入端口，即鸢尾花特征输入。 输出的选择取决于对预测的类（评分标签）、评分概率还是两者都感兴趣。 在本示例中，假设用户对两者都感兴趣。 若要选择所需的输出列，请使用[选择数据集中的列][select-columns]模块。 单击[选择数据集中的列][select-columns]，单击“启动列选择器”****，并选择“评分标签”**** 和“评分概率”****。 设置[选择数据集中的列][select-columns]的输出端口并再次运行它之后，应准备好通过单击“发布 Web 服务”**** 将评分实验发布为 Web 服务。 最终实验类似于图 4。

![鸢尾花双类分类实验](./media/interpret-model-results/4.png)

图 4。 鸢尾花双类分类问题的最终评分实验

运行 Web 服务并输入测试实例的某些特征值后，结果返回两个数字。 第一个数字是评分标签，第二个数字是评分概率。 此花卉预测为类 1，概率为 0.9655。

![测试解释评分模型](./media/interpret-model-results/4_1.png)

![评分测试结果](./media/interpret-model-results/5.png)

图 5。 鸢尾花双类分类的 Web 服务结果

### <a name="multi-class-classification"></a>多类分类
**示例实验**

在此实验中，执行字母识别任务作为多类分类的示例。 分类器尝试基于某些从手写图像中提取的手写属性值预测特定字母（类）。

![字母识别示例](./media/interpret-model-results/5_1.png)

在训练数据中，有 16 个从手写字母图像中提取的特征。 26 个字母形成了 26 个类。 图 6 显示一个实验，该实验将训练多类分类模型进行字母识别，并对测试数据集上的相同特征集进行预测。

![字母识别多类分类实验](./media/interpret-model-results/6.png)

图 6。 字母识别多类分类实验问题

可视化[评分模型][score-model]模块的结果，方法是单击[评分模型][score-model]模块的输出端口，并单击“可视化”****，应看到如图 7 所示的内容。

![评分模型结果](./media/interpret-model-results/7.png)

图 7。 可视化多类分类中的评分模型结果

**结果解释**

左边的 16 个列表示测试集的特征值。 名称为“类“XX”的评分概率”之类的列类似于双类案例中的“评分概率”列。 它们显示对应的项归入特定类的概率。 例如，对于第一个条目，出现0.003571 个概率，即 "A"、"0.000451" 的概率，即 "B" 等。 最后一列（评分标签）与双类案例中的评分标签相同。 它选择具有最大评分概率的类作为对应项的预测类。 例如，对于第一个条目，评分标签为 "F"，因为它的概率最大为 "F" （0.916995）。

**Web 服务发布**

还可获取每个项的评分标签和评分标签的概率。 基本逻辑是查找所有评分概率中最大的概率。 若要执行此操作，需要使用[执行 R 脚本][execute-r-script]模块。 R 代码显示在图 8 中，实验结果显示在图 9 中。

![R 代码示例](./media/interpret-model-results/8.png)

图 8。 用于提取评分标签及标签的关联概率的 R 代码

![实验结果](./media/interpret-model-results/9.png)

图 9. 字母识别多类分类问题的最终评分实验

发布和运行 Web 服务并输入某些输入特征值后，返回的结果类似于图 10。 此手写信（其提取的16项功能）预测为 "T"，其中包含0.9715 的概率。

![测试解释评分模块](./media/interpret-model-results/9_1.png)

![测试结果](./media/interpret-model-results/10.png)

图 10. 多类分类的 Web 服务结果

## <a name="regression"></a>回归测试
回归问题不同于分类问题。 在分类问题中，将尝试预测离散类，如鸢尾花所属的类。 但是，正如以下回归问题示例所示，将尝试预测连续变量，如一辆汽车的价格。

**示例实验**

使用汽车价格预测作为回归的示例。 将尝试根据特征预测汽车的价格，包括型号、燃料类型、车身类型和驱动轮。 实验显示在图 11 中。

![汽车价格回归实验](./media/interpret-model-results/11.png)

图 11. 汽车价格回归问题实验

可视化[评分模型][score-model]模块，结果类似于图 12。

![汽车价格预测问题的评分结果](./media/interpret-model-results/12.png)

图 12. 汽车价格预测问题的评分结果

**结果解释**

评分标签是此评分结果中的结果列。 数字是每辆车的预测价格。

**Web 服务发布**

可将回归实验发布到 Web 服务中，并调用它进行汽车价格预测，与双类分类用例方法相同。

![汽车价格回归问题的评分实验](./media/interpret-model-results/13.png)

图 13. 汽车价格回归问题的评分实验

运行 Web 服务，返回的结果类似于图 14。 此汽车的预测价格为 15085.52 美元。

![测试解释评分模块](./media/interpret-model-results/13_1.png)

![评分模块结果](./media/interpret-model-results/14.png)

图 14. 汽车价格回归问题的 Web 服务结果

## <a name="clustering"></a>聚类分析
**示例实验**

让我们再次使用 Iris 数据集生成聚类分析试验。 可在此处筛选出数据集中的类标签，以便它仅具有特征，并且可用于聚类。 在此鸢尾花用例中，在训练过程中将群集的数量指定为二，这意味着将花卉聚类为两个类。 实验显示在图 15 中。

![鸢尾花聚类问题实验](./media/interpret-model-results/15.png)

图 15. 鸢尾花聚类问题实验

群集不同于分类的不同之处在于，定型数据集本身并没有实际的标签。 聚类将训练数据集实例分组为离散群集。 在训练过程中，模型通过了解项特征之间的差异标记项。 在那之后，训练的模型可用于进一步分类将来的项。 在聚类问题中，我们对结果的两个部分感兴趣。 第一个部分是训练数据集，第二个部分是使用训练的模型为新数据集分类。

结果的第一个部分可以可视化，方法是单击[聚类分析模型定型][train-clustering-model]的左输出端口，并单击“可视化”****。 可视化显示在图 16 中。

![聚类结果](./media/interpret-model-results/16.png)

图 16. 可视化训练数据集的聚类结果

第二个部分（使用训练的聚类模型聚类新项）显示在图 17 中。

![可视化聚类结果](./media/interpret-model-results/17.png)

图 17. 可视化新数据集的聚类结果

**结果解释**

尽管两个部分的结果源于不同实验阶段，但它们看起来相同，并且以相同方式解释。 前四列是功能。 最后一列“分配”是预测结果。 分配有相同数字的项预测为在同一个群集中，即，它们在某些方面具有相似性（此实验使用默认的欧几里德距离度量）。 由于已将群集的数量指定为 2，因此“分配”中的项标记为 0 或 1。

**Web 服务发布**

可将聚类实验发布到 Web 服务中，并调用它进行聚类预测，与双类分类用例方法相同。

![鸢尾花聚类问题的评分实验](./media/interpret-model-results/18.png)

图 18. 鸢尾花聚类问题的评分实验

运行 Web 服务后，返回的结果类似于图 19。 此花卉预测为在群集 0 中。

![测试解释评分模块](./media/interpret-model-results/18_1.png)

![评分模块结果](./media/interpret-model-results/19.png)

图 19. 鸢尾花双类分类的 Web 服务结果

## <a name="recommender-system"></a>推荐器系统
**示例实验**

对于推荐器系统，可使用餐厅推荐问题作为示例：可基于评级历史记录为客户推荐餐厅。 输入数据由三部分组成：

* 来自客户的餐厅评级
* 客户特征数据
* 餐馆特色数据

使用 Azure 机器学习工作室（经典版）中的 [Matchbox 推荐器定型][train-matchbox-recommender]模块，可以执行多个操作：

* 预测给定用户和项目的评级
* 向给定用户推荐项目
* 查找与给定用户相关的用户
* 查找与给定项目相关的项目

通过从“推荐器预测类型”**** 菜单中的四种选项中选择，可选择要执行的操作。 下面演练全部四种方案。

![Matchbox 推荐器](./media/interpret-model-results/19_1.png)

推荐器系统的典型 Azure 机器学习工作室（经典版）实验类似于图 20。 有关如何使用这些推荐器系统模块的信息，请参阅 [Matchbox 推荐器定型][train-matchbox-recommender]和 [Matchbox 推荐器评分][score-matchbox-recommender]。

![推荐器系统实验](./media/interpret-model-results/20.png)

图 20. 推荐器系统实验

**结果解释**

**预测给定用户和项目的评级**

选择“推荐器预测类型”**** 下的“评分预测”**** 即要求推荐器系统预测给定用户和项目的评级。 [Matchbox 推荐器评分][score-matchbox-recommender]输出的可视化类似于图 21。

![推荐器系统的评分结果 - 评级预测](./media/interpret-model-results/21.png)

图 21. 可视化推荐器系统的评分结果 - 评级预测

前两列是输入数据提供的用户项目对。 第三列是用户对特定项目的预测评级。 例如，在第一行中，预测客户 U1048 将餐厅 135026 评级为 2。

**向给定用户推荐项目**

选择“推荐器预测类型”**** 下的“项目推荐”**** 即要求推荐器系统向给定用户推荐项目。 此方案中要选择的最后一个参数是*推荐项目选择*。 选项“从评级项目(用于模型评估)”**** 主要用于训练过程中的模型评估。 对于此预测阶段，我们选择“从所有项目”****。 [Matchbox 推荐器评分][score-matchbox-recommender]输出的可视化类似于图 22。

![推荐器系统的评分结果 - 项目推荐](./media/interpret-model-results/22.png)

图 22. 可视化推荐器系统的评分结果 - 项目推荐

六列中的第一列表示向要为其推荐项目的给定用户 ID，由输入数据提供。 其他五列表示向该用户推荐的项目，以相关度降序排序。 例如，在第一行中，对客户 U1048 最推荐的餐厅为 134986，然后依次为 135018、134975、135021 和 132862。

**查找与给定用户相关的用户**

选择“推荐器预测类型”**** 下的“相关用户”**** 即要求推荐器系统查找给定用户的相关用户。 相关用户是具有相似偏好的用户。 此方案中要选择的最后一个参数是*相关用户选择*。 选项“从已为项目评级的用户(用于模型评估)”**** 主要用于训练过程中的模型评估。 对于此预测阶段，选择“从所有用户”****。 [Matchbox 推荐器评分][score-matchbox-recommender]输出的可视化类似于图 23。

![推荐器系统的评分结果 --相关用户](./media/interpret-model-results/23.png)

图 23. 可视化推荐器系统的评分结果--相关用户

六列中的第一列显示查找相关用户所需的给定用户 ID，由输入数据提供。 其他五列存储该用户的预测相关用户，以相关度降序排序。 例如，在第一行中，客户 U1048 最相关的客户为 U1051，然后依次为 U1066、U1044、U1017 和 U1072。

**查找与给定项目相关的项目**

选择“推荐器预测类型”**** 下的“相关项目”**** 即要求推荐器系统查找给定项目的相关项目。 相关项目是同一个用户最有可能喜欢的项目。 此方案中要选择的最后一个参数是*相关项目选择*。 选项“从评级项目(用于模型评估)”**** 主要用于训练过程中的模型评估。 对于此预测阶段，我们选择“从所有项目”****。 [Matchbox 推荐器评分][score-matchbox-recommender]输出的可视化类似于图 24。

![推荐器系统的评分结果 --相关项目](./media/interpret-model-results/24.png)

图 24. 可视化推荐器系统的评分结果--相关项目

六列中的第一列表示查找相关项目所需的给定项目 ID，由输入数据提供。 其他五列存储该项目的预测相关项目，以相关度降序排序。 例如，在第一行中，项目 135026 最相关的项目为 135074，然后依次为 135035、132875、135055 和 134992。

**Web 服务发布**

对于这四个方案中的每一个，将这些实验发布为 Web 服务以获取预测的过程都类似。 此处我们以第二个方案（向给定用户的推荐项目）为例。 对于其他三个方案，可遵循相同的过程。

将训练的推荐器系统保存为训练的模型并根据请求将输入数据筛选到单个用户 ID 列，可挂钩该实验（如图 25 所示）并将其发布为 Web 服务。

![餐厅推荐问题的评分实验](./media/interpret-model-results/25.png)

图 25. 餐厅推荐问题的评分实验

运行 Web 服务，返回的结果类似于图 26。 对用户 U1048 最推荐的五个餐厅为 134986、135018、134975、135021 和 132862。

![推荐器系统服务的示例](./media/interpret-model-results/25_1.png)

![示例实验结果](./media/interpret-model-results/26.png)

图 26. 餐厅推荐问题的 Web 服务结果

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
