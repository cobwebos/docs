---
title: "在机器学习中评估模型性能 | Microsoft Docs"
description: "说明如何在 Azure 机器学习中评估模型性能。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: 46c624608773fcd55e7ee3548aaa32fbbc630056
ms.openlocfilehash: b6d0c877e0f0515dd82c0615850009910aa0b0be
ms.lasthandoff: 02/07/2017


---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>如何在 Azure 机器学习中评估模型性能
本文演示了如何在 Azure 机器学习工作室中评估模型的性能，并提供可用于此任务的指标的简短说明。 显示了三个常见的监督学习方案： 

* 回归
* 二元分类 
* 多类分类

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

评估模型性能是数据科学过程的核心阶段之一。 它指示训练的模型为某个数据集评分的成功程度。 

Azure 机器学习通过其两个主要的机器学习模块支持模型评估：[评估模型][evaluate-model]和[交叉验证模型][cross-validate-model]。 这些模型允许查看模型在一些机器学习中常用的指标和统计信息方面性能如何。

## <a name="evaluation-vs-cross-validation"></a>评估与交叉验证
评估和交叉验证是测量模型性能的标准方法。 它们都生成可检查或与其他模型比较的评估指标。

[评估模型][evaluate-model]预期评分数据集为输入（如果要比较 2 个不同模型的性能，则为 2）。 这意味着需要使用[训练模型][train-model]模块训练模型并使用[评分模型][score-model]模块对某些数据集进行预测，然后才能评估结果。 评估基于评分标签/概率以及真实标签，所有这些均由[评分模型][score-model]模块输出。

此外，可使用交叉验证对输入数据的不同子集自动执行大量训练-评分-评估操作（10 折）。 输入数据拆分为 10 个部分，其中一个部分保留用于测试，其他 9 个部分用于训练。 此过程重复 10 次，取评估指标的平均值。 这有助于确定模型对新数据集的一般化程度。 [交叉验证模型][cross-validate-model]模块接受未训练的模型和某些已标记的数据集，并且除了平均结果以外，还输出 10 折中每一折的评估结果。

在以下部分中，我们将使用[评估模型][evaluate-model]和[交叉验证模型][cross-validate-model]模块生成简单的回归和分类模型，并评估其性能。

## <a name="evaluating-a-regression-model"></a>评估回归模型
假设我们要使用某些特征（如尺寸、马力、引擎规格等）预测一辆汽车的价格。 这是典型的回归问题，其中目标变量（*价格*）是一个连续数值。 我们可以采用一个简单的线性回归模型，该模型可在给定特定汽车的特征值的情况下预测该汽车的价格。 此回归模型可用于为我们训练所用的相同数据集评分。 得到所有汽车的预测价格后，我们可以通过查看预测与实际价格的平均偏离程度来评估模型的性能。 为了演示此操作，我们使用 Azure 机器学习工作室的“已保存数据集”部分中提供的*汽车价格数据（原始）数据集*。

### <a name="creating-the-experiment"></a>创建实验
在 Azure 机器学习工作室中将以下模块添加到工作区：

* 汽车价格数据（原始）
* [线性回归][linear-regression]
* [训练模型][train-model]
* [评分模型][score-model]
* [评估模型][evaluate-model]

如下面的图 1 所示连接端口，并将[训练模型][train-model]模块的“标签”列设置为“价格”。

![评估回归模型](media/machine-learning-evaluate-model-performance/1.png)

图 1. 评估回归模型。

### <a name="inspecting-the-evaluation-results"></a>检查评估结果
运行实验后，可单击[评估模型][evaluate-model]模块的输出端口，并选择“可视化”查看评估结果。 可用于回归模型的评估指标包括：*平均绝对误差*、*根平均绝对误差*、*相对绝对误差*、*相对方误差*和*决定系数*。

术语“误差”在此处表示预测值和真实值之间的差。 通常计算此差的绝对值或平方来捕获所有实例上的误差总量，因为预测值和真实值之间的差在某些情况下可能为负。 误差指标通过预测与真实值之间的平均偏差来测量回归模型的预测性能。 误差值较低意味着模型在预测方面更准确。 总体错误指标为 0 意味着模型与数据完美拟合。

决定系数（也称为 R 平方）也是测量模型与数据拟合程度的标准方法。 它可以解释为变化中由模型解释的比例。 在此情况下比例越高越好，其中 1 表示完美拟合。

![线性回归评估指标](media/machine-learning-evaluate-model-performance/2.png)

图 2。 线性回归评估指标。

### <a name="using-cross-validation"></a>使用交叉验证
如前所示，可使用[交叉验证模型][cross-validate-model]模块自动执行重复培训、评分和评估。 在此情况下只需要一个数据集、一个未训练的模型和一个[交叉验证模型][cross-validate-model]模块（请参阅下图）。 请注意，需要在[交叉验证模型][cross-validate-model]模块的属性中将标签列设置为“价格”。

![交叉验证回归模型](media/machine-learning-evaluate-model-performance/3.png)

图 3。 交叉验证回归模型。

运行实验后，可通过单击[交叉验证模型][cross-validate-model]模块的右输出端口检查评估结果。 这将提供每次迭代（折）的指标的详细视图和每个指标的平均结果（图 4）。

![回归模型的交叉验证结果](media/machine-learning-evaluate-model-performance/4.png)

图 4。 回归模型的交叉验证结果。

## <a name="evaluating-a-binary-classification-model"></a>评估二元分类模型
在二元分类方案中，目标变量只有两个可能的结果，例如： {0, 1} 或 {false, true}、{negative, positive}。 假设为你提供一个具有人口统计和雇用变量的成年员工数据集，并要求你预测收入级别，它是一个带有值的二进制变量 {"< =&50;k"，">&50;k"}。 换言之，负类表示每年收入小于或等于 50K 的员工，正类表示所有其他员工。 和在回归方案中一样，我们可以训练一个模型、评分一些数据，然后评估结果。 此处的主要区别是 Azure 机器学习计算并输出的所选指标。 为了演示收入级别预测方案，我们将使用[成年人](http://archive.ics.uci.edu/ml/datasets/Adult)数据集创建 Azure 机器学习实验并评估双类逻辑回归模型（一种常用的二元分类器）。

### <a name="creating-the-experiment"></a>创建实验
在 Azure 机器学习工作室中将以下模块添加到工作区：

* 成年人口收入二元分类数据集
* [两类逻辑回归][two-class-logistic-regression]
* [训练模型][train-model]
* [评分模型][score-model]
* [评估模型][evaluate-model]

如下面的图 5 所示连接端口，并将[训练模型][train-model]模块的“标签”列设置为“收入”。

![评估二元分类模型](media/machine-learning-evaluate-model-performance/5.png)

图 5。 评估二元分类模型。

### <a name="inspecting-the-evaluation-results"></a>检查评估结果
运行实验后，可单击[评估模型][evaluate-model]模块的输出端口，并选择“可视化”查看评估结果（图 7）。 可用于二元分类模型的评估指标包括：*准确性*、*精度*、*召回率*、*F1 分数*和 *AUC*。 此外，模块输出一个显示真正、假负、假正和真负的混淆矩阵，以及 *ROC*、*精度/召回率*和*升力*曲线。

准确性就是正确分类的实例的比例。 它通常是评估分类器时所查看的第一个指标。 但是，当测试数据不平衡（即大部分实例属于其中一个类），或者你对其中任一类上的性能更感兴趣时，准确性无法真正捕获分类器的有效性。 在收入级别分类器方案中，假设在测试的数据中，99% 的实例代表每年收入小于或等于 50K 的人。 通过对所有实例预测类“<=50K”，可能实现 0.99 的准确性。 此情况下的分类器看起来总体上表现不错，但实际上，它未能正确分类任何高收入个体（那 1% 的人）。

因此，计算捕获评估的更具体方面的其他指标很有帮助。 在详细讨论此类指标之前，了解二元分类评估的混淆矩阵很重要。 训练集中的类标签只能采用 2 个可能的值，我们通常称其为正或负。 分类器正确预测的正和负实例分别称为真正 (TP) 和真负 (TN)。 同样，错误分类的实例称为假正和 (FP) 和假负 (FN)。 混淆矩阵就是一个显示归入这 4 个类别中的每一个的实例数量的表。 Azure 机器学习自动确定数据集中哪两个类为正类。 如果类标签是布尔值或整数，则向标记为“true”或“1”的实例分配正类。 如果标签是字符串（如收入数据集的情况），则标签按字母顺序排序，并且第一个级别选择为负类，而第二个级别为正类。

![二元分类混淆矩阵](media/machine-learning-evaluate-model-performance/6a.png)

图 6。 二元分类混淆矩阵。

回到收入分类问题，我们可能要问若干个评估问题，以帮助我们了解所使用的分类器的性能。 一个自然而然的问题是：“在模型预测收入 >50K 的个体中 (TP+FP)，有多少分类正确 (TP)？” 可通过查看模型的**精度**来回答此问题，该指标是分类正确的正的比例：TP/(TP+FP)。 另一个常见问题是“在所有收入&50;k 的高收入员工中 (TP+FN)，分类器正确分类了多少 (TP)”。 这实际上是**召回率**或真正率：分类器的 TP/(TP+FN)。 你可能注意到精度和召回率之间存在明显的权衡。 例如，给定一个相对均衡的数据集，预测大多数正实例的分类器将具有高召回率，但精度较低，因为许多负实例将被错误分类，从而导致大量假正。 若要查看这两个指标如何变化的图，可单击评估结果输出页中的“精度/召回率”曲线（图 7 左上角）。

![二元分类评估结果](media/machine-learning-evaluate-model-performance/7.png)

图 7。 二元分类评估结果。

另一个常用的相关指标是 **F1 分数**，该指标同时考虑精度和召回率。 它是这两个指标的调和平均值，计算方式如下：F1 = 2（精度 x 召回率）/（精度 + 召回率）。 F1 分数是以单个数字总结评估的好方法，但是共同查看精度和召回率以便更好地了解分类器行为始终是良好的做法。

此外，可在“受试者工作特性曲线 (ROC)”曲线和对应的“曲线下面积 (AUC)”值中检查真正与假正的比率。 此曲线离左上角越近，分类器的性能越好（即，最大程度提高真正率，同时最大程度减少负正率）。 离图的对角线很近的曲线，产生自倾向于进行接近随机猜测的预测的分类器。

### <a name="using-cross-validation"></a>使用交叉验证
和在回归示例中一样，我们执行交叉验证以自动重复训练、评分和评估数据的不同子集。 同样，我们可以使用[交叉验证模型][cross-validate-model]模块、一个未训练的逻辑回归模型和一个数据集。 必须在[交叉验证模型][cross-validate-model]模块的属性中将标签列设置为“收入”。 运行实验并单击[交叉验证模型][cross-validate-model]模块的右输出端口后，除了每一折的平均和标准偏差，我们可以看到每一折的二元分类指标值。 

![交叉验证二元分类模型](media/machine-learning-evaluate-model-performance/8.png)

图 8。 交叉验证二元分类模型。

![二元分类器交叉验证结果](media/machine-learning-evaluate-model-performance/9.png)

图 9。 二元分类器的交叉验证结果。

## <a name="evaluating-a-multiclass-classification-model"></a>评估多类分类模型
在此实验中，我们将使用流行的[鸢尾花](http://archive.ics.uci.edu/ml/datasets/Iris "Iris")数据集，其中包含 3 种不同类型（类）的鸢尾属植物的实例。 每个实例有 4 个特征值（花萼长度/宽度和花瓣长度/宽度）。 在之前的实例中，我们使用相同的数据集训练并测试了模型。 此处，我们将使用[拆分数据][split]模块创建数据的 2 个子集、对第一个子集训练，然后对第二个子集评分和评估。 鸢尾花数据集在 [UCI 机器学习存储库](http://archive.ics.uci.edu/ml/index.html)中公开提供，并且可使用[导入数据][import-data]模块下载。

### <a name="creating-the-experiment"></a>创建实验
在 Azure 机器学习工作室中将以下模块添加到工作区：

* [导入数据][import-data]
* [多类决策林][multiclass-decision-forest]
* [拆分数据][split]
* [训练模型][train-model]
* [评分模型][score-model]
* [评估模型][evaluate-model]

连接端口，如下面的图 10 所示。

将[训练模型][train-model]模块的标签列索引设置为 5。 数据集不再具有标题行，但我们知道类标签在第五列。

单击[导入数据][import-data]模块，将“数据源”属性设置为“通过 HTTP 的 Web URL”，将“URL”设置为 http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data。

在[拆分数据][split]模块中将部分实例设置为用于训练（例如&0;.7）。

![评估多类分类器](media/machine-learning-evaluate-model-performance/10.png)

图 10. 评估多类分类器

### <a name="inspecting-the-evaluation-results"></a>检查评估结果
运行实验并单击[评估模型][evaluate-model]的输出端口。 在此情况下，评估结果以混淆矩阵形式显示。 矩阵显示所有 3 个类的实际和预测实例。

![多类分类评估结果](media/machine-learning-evaluate-model-performance/11.png)

图 11. 多类分类评估结果。

### <a name="using-cross-validation"></a>使用交叉验证
如前所示，可使用[交叉验证模型][cross-validate-model]模块自动执行重复培训、评分和评估。 将需要一个数据集、一个未训练的模型和一个[交叉验证模型][cross-validate-model]模块（请参阅下图）。 再次需要设置[交叉验证模型][cross-validate-model]模块的标签列（在此情况下为列索引 5）。 运行实验并单击[交叉验证模型][cross-validate-model]模块的右输出端口后，可检查每一折的指标值以及平均和标准偏差。 此处显示的指标类似于二元分类案例中讨论的指标。 但是，请注意在多类分析中，通过按每类计数来计算真正/负和假正/负，因为不存在总体的正或负类。 例如，当计算“Iris-setosa”类的精度或召回率时，假设这是正类，其他全部为负类。

![交叉验证多类分类模型](media/machine-learning-evaluate-model-performance/12.png)

图 12. 交叉验证多类分类模型。

![多类分类模型的交叉验证结果](media/machine-learning-evaluate-model-performance/13.png)

图 13. 多类分类模型的交叉验证结果。

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/


