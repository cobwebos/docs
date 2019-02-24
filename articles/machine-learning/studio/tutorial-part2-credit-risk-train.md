---
title: 教程 2：训练信用风险模型
titleSuffix: Azure Machine Learning Studio
description: 本详细教程介绍如何创建预测分析解决方案，用于在 Azure 机器学习工作室中进行信用风险评估。 本教程是由三个部分构成的系列教程的第二部分， 其中介绍了如何训练和评估模型。
keywords: 信用风险, 预测分析解决方案, 风险评估
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 45407e183c70fe67e6bd59e3fd86a50a31844c47
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453471"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio"></a>教程 2：训练信用风险模型 - Azure 机器学习工作室

在本教程中，我们将深入探讨开发预测分析解决方案的过程。 我们将在机器学习工作室中开发一个简单模型。  然后将该模型部署为 Azure 机器学习 Web 服务。  部署的模型将使用新数据进行预测。 本教程是**由三个部分构成的系列教程的第二部分**。

假设用户需要根据他们提供的贷款申请相关信息预测个人的信用风险。  

信用风险评估是个较为复杂的问题，但本教程会将其适当简化。 我们将使用它作为示例，展示如何使用 Microsoft Azure 机器学习工作室来创建预测分析解决方案。 对此解决方案，我们将使用 Azure 机器学习工作室和机器学习 Web 服务。  

在这篇由三个部分构成的教程中，我们将从公开的信用风险数据着手。  然后开发并训练预测模型。  最后将该模型部署为 Web 服务。

在[本教程的第一部分](tutorial-part1-credit-risk.md)中，我们已创建了一个机器学习工作室工作区、上传了数据，并创建了试验。

本教程部分介绍以下操作：
 
> [!div class="checklist"]
> * 训练多个模型
> * 评分和评估模型


在[本教程的第三部分](tutorial-part3-credit-risk-deploy.md)，我们要将此模型部署为 Web 服务。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>先决条件

完成[本教程的第一部分](tutorial-part1-credit-risk.md)。

## <a name="train"></a>训练多个模型

使用 Azure 机器学习工作室创建机器学习模型的优势之一是，能够在单个实验中一次性尝试多种模型并比较结果。 此类型的实验有助于找到最适合解决问题的解决方案。

在本教程开发的试验中，你将创建两个不同类型的模型，然后比较其评分结果，从而确定我们希望用于最终试验的算法。  

可从多种模型中进行选择。 要查看可用的模型，请在模块调色板中展开“机器学习”节点，并展开“初始化模型”及其下面的节点。 为实现本试验的目的，我们将选择[两类支持向量机][two-class-support-vector-machine] (SVM) 和[两类提升决策树][two-class-boosted-decision-tree]模块。

> [!TIP]
> 要获取有关确定哪种机器学习算法最适合你正尝试解决的特定问题的帮助，请参阅[如何选择 Microsoft Azure 机器学习工作室的算法](algorithm-choice.md)。
> 
> 

在此试验中，我们将同时添加[两类提升决策树][two-class-boosted-decision-tree]模块和[两类支持向量机][two-class-support-vector-machine]模块。

### <a name="two-class-boosted-decision-tree"></a>两类提升决策树

首先设置提升决策树模型。

1. 在模块面板中找到[两类提升决策树][two-class-boosted-decision-tree]模块，并将其拖动到画布上。

1. 找到[训练模型][train-model]模块、将其拖动到画布上，然后将[两类提升决策树][two-class-boosted-decision-tree]的输出连接到[训练模型][train-model]模块的左侧输入端口。
   
   [两类提升决策树][two-class-boosted-decision-tree]模块初始化泛型模型，[训练模型][train-model]使用训练数据来训练模型。 

1. 将左侧[执行 R 脚本][execute-r-script]模块的左侧输出连接到[训练模型][train-model]模块的右侧输入端口（在本教程中，我们[使用了“拆分数据”模块左侧传出的数据](#train)进行训练）。
   
   > [!TIP]
   > 此试验不需要[执行 R 脚本][execute-r-script]模块的两个输入和一个输出，因此我们将其保留为未附加状态。 
   > 
   > 

实验的此部分现在如下所示：  

![训练模型](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

现在，我们需要告诉[训练模型][train-model]我们希望它预测信用风险值。

1. 选择[训练模型][train-model]模块。 在“属性”窗格中，单击“启动列选择器”。

1. 在“选择单个列”对话框中，在“可用列”下的搜索字段中键入“信用风险”，在下方选择“信用风险”，并单击向右箭头按钮 (**>**) 将“信用风险”移动到“选定列”。 

    ![选择“训练模型”模块的“信用风险”列](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. 单击“确定”复选标记。

### <a name="two-class-support-vector-machine"></a>两类支持向量机

接下来设置 SVM 模型。  

首先，简要介绍 SVM。 提升决策树非常适用于所有类型的功能。 但是，因为 SVM 模块生成一个线性分类器，因此它生成的模型在所有数值功能具有相同范围时存在最佳测试错误。 若要使所有数值功能转换为同一范围，请使用“Tanh”转换（具有[规范化数据][normalize-data]模块）。 这会将我们的数字转换为 [0,1] 范围。 SVM 模块将字符串功能依次转换为分类功能和二进制 0/1 功能，因此无需手动转换字符串功能。 此外，也不需要转换“信用风险”列（列 21）- 它是数值，但也是我们在训练模型时要预测的值，因此需要使其保持不变。  

若要设置 SVM 模型，请执行以下操作：

1. 在模块面板中找到[两类支持向量机][two-class-support-vector-machine]模块，并将其拖动到画布上。

1. 右键单击[训练模型][train-model]模块，选择“复制”，并右键单击画布并选择“粘贴”。 [训练模型][train-model]模块的副本具有与原始模块相同的列选择。

1. 将[两类支持向量机][two-class-support-vector-machine]模块的输出连接到第二个[训练模型][train-model]模块的左侧输入端口。

1. 查找[规范化数据][normalize-data]模块并将其拖动到画布上。

1. 将左侧[执行 R 脚本][execute-r-script]模块的左侧输出连接到此模块的输入（请注意，模块的输出端口可以连接到多个其他模块）。

1. 将[规范化数据][normalize-data]模块的左侧输出端口连接到第二个[训练模型][train-model]模块的右侧输入端口。

我们实验的此部分现在应如下所示：  

![训练第二个模型](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

现在，配置[规范化数据][normalize-data]模块：

1. 单击以选择[规范化数据][normalize-data]模块。 在“属性”窗格中，选择“Tanh”作为“转换方法”参数。

1. 单击“启动列选择器”、为“开始”选择“没有列”、在第一个下拉列表中选择“包括”、在第二个下拉列表中选择“列类型”，并在第三个下拉列表中选择“数值”。 这会指定所有数值列（和唯一数值）均已转换。

1. 单击此行右侧的加号 (+)，这会创建一行下拉列表。 在第一个下拉列表中选择“排除”，在第二个下拉列表中选择“列名称”，并在文本字段中输入“信用风险”。 这会指定应忽略“信用风险”列（我们需要执行此操作，因为此列是数值，因此，如果不排除，也将转换）。

1. 单击“确定”复选标记。  

    ![选择用于“规范化数据”模块的列](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


[规范化数据][normalize-data]模块现在设置为在所有数值列（“信用风险”列除外）上执行 Tanh 转换。  

## <a name="score-and-evaluate-the-models"></a>评分和评估模型

使用已由[拆分数据][split]模块分隔出的测试数据对已训练的模型进行评分。 然后，可以比较两个模型的结果，查看哪个模型生成的结果更好。  

### <a name="add-the-score-model-modules"></a>添加“评分模型”模块

1. 找到[评分模型][score-model]模块并将其拖动到画布上。

1. 将已连接到[两类提升决策树][two-class-boosted-decision-tree]模块的[训练模型][train-model]模块连接到[评分模型][score-model]模块的左侧输入端口。

1. 将右侧[执行 R 脚本][execute-r-script]模块（我们的测试数据）连接到[评分模型][score-model]模块的右侧输入端口。

    ![已连接的“评分模型”模块](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   [评分模型][score-model]模块现在可以从测试数据中获取信用信息、通过模型运行它，并比较模型生成的预测与测试数据中的实际信用风险列。

1. 复制并粘贴[评分模型][score-model]模块以创建第二个副本。

1. 将 SVM 模型的输出（即连接到[两类支持向量机][two-class-support-vector-machine]模块的[训练模型][train-model]模块的输出端口）连接到第二个[评分模型][score-model]模块的输入端口。

1. 对于 SVM 模型，我们需要像处理训练数据一样，对测试数据执行相同的转换。 因此，请复制并粘贴[规范化数据][normalize-data]模块以创建第二个副本，并将其连接到右侧[执行 R 脚本][execute-r-script]模块。

1. 将第二个[规范化数据][normalize-data]模块的左侧输出连接到第二个[评分模型][score-model]模块的右侧输入端口。

    ![已连接的两个“评分模型”模块](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>添加“评估模型”模块

若要评估两个评分结果并对其进行比较，请使用[评估模型][evaluate-model]模块。  

1. 找到[评估模型][evaluate-model]模块并将其拖动到画布上。

1. 将与提升决策树模型相关联的[评分模型][score-model]模块的输出端口连接到[评估模型][evaluate-model]模块的左侧输入端口。

1. 将另一个[评分模型][score-model]模块连接到右侧输入端口。  

    ![已连接的“评估模型”模块](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>运行实验并检查结果

若要运行此实验，请单击画布下面的“运行”按钮。 可能需要几分钟时间。 每个模块上的旋转指示符显示它正在运行，模块完成后，会显示一个绿色对号。 当所有模块都有一个对号时，表示该实验已完成运行。

实验现在看起来应当与下图类似：  

![评估两种模型](./media/tutorial-part2-credit-risk-train/final-experiment.png)


要检查结果，请单击[评估模型][evaluate-model]模块的输出端口，并选择“可视化”。  

[评估模型][evaluate-model]模块将生成一对曲线和度量值，从而比较两个已评分模型的结果。 可将结果视为受试者工作特征 (ROC) 曲线、精度/召回曲线或提升曲线。 其他显示数据包括混淆矩阵、曲线下面积 (AUC) 的累积值和其他度量值。 可通过将滑块向左或向右移动更改阈值，并查看它如何影响度量值集。  

在图表右侧，单击“已评分数据”或“要比较的已评分数据集”，突出显示关联的曲线并在下方显示关联的度量值。 在曲线图例中，“已评分数据集”对应于[评估模型][evaluate-model]模块的左侧输入端口；在本例中，这是提升决策树模型。 “要比较的已评分数据集”对应于右侧输入端口；在本例中，这是 SVM 模型。 单击其中一个标签后，该模型的曲线将突出显示，并且会显示相应的度量值，如下图所示。  

![模型的 ROC 曲线](./media/tutorial-part2-credit-risk-train/roc-curves.png)

通过检查这些值，可以确定哪个模型提供的结果与你所需的结果最接近。 可以返回，通过更改不同模型中的参数值来迭代进行实验。 

对这些结果进行解释以及对模型性能进行优化的科学与艺术不在本教程的范围内。 若要获得更多帮助，可以阅读以下文章：
- [如何在 Azure 机器学习工作室中评估模型性能](evaluate-model-performance.md)
- [选择参数优化 Azure 机器学习工作室中的算法](algorithm-parameters-optimize.md)
- [在 Azure 机器学习工作室中解释模型结果](interpret-model-results.md)

> [!TIP]
> 每次运行实验时，该迭代的记录都会保留在运行历史记录中。 可以通过单击画布下面的“查看运行历史记录”查看这些迭代，并返回到任何一个迭代。 也可以单击“属性”窗格中的“之前运行”，返回到已打开迭代的前一个迭代。
> 
> 可通过单击画布下面的“另存为”，复制实验的任何迭代。 
> 使用试验的“摘要”和“说明”属性，保留在试验迭代中所尝试操作的记录。
> 
> 有关详细信息，请参阅 [在 Azure 机器学习工作室中管理试验迭代](manage-experiment-iterations.md)。  
> 
> 

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，我们已完成以下步骤： 
 
> [!div class="checklist"]
> * 创建试验
> * 训练多个模型
> * 评分和评估模型

现在，可以部署此数据的模型。

> [!div class="nextstepaction"]
> [教程 3 - 部署模型](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/