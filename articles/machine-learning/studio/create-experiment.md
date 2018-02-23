---
title: "机器学习工作室中的简单试验 | Microsoft Docs"
description: "本机器学习教程逐步讲解如何轻松完成数据科研试验。 使用回归算法预测汽车价格。"
keywords: "试验, 线性回归, 机器学习算法, 机器学习教程, 预测建模技术, 数据科研试验"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: 3cc4278ca7d4603f4bfb916885d44e6a85864b25
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>机器学习教程：在 Azure 机器学习工作室中创建第一个数据科研试验

本教程适合从未使用过 **Azure 机器学习工作室**的用户。

本教程介绍如何在首次使用工作室时创建机器学习试验。 此试验将测试一个分析模型，该模型使用制造商和技术规格等各种变量来预测汽车的价格。

> [!NOTE]
> 本教程介绍各种基础知识：如何将模块拖放到试验中、如何将模块连接到一起、如何运行试验，以及如何查看结果。 我们不讨论机器学习这一宽泛主题，也不讨论如何选择和使用工作室中包括的 100 多种内置算法和数据操作模块。
>
>如果不熟悉机器学习，可先观看视频系列：[适合初学者的数据科学](data-science-for-beginners-the-5-questions-data-science-answers.md)。 该视频系列使用日常语言和概念对机器学习进行了很好的介绍。
>
>如果已熟悉机器学习，但想获取有关机器学习工作室的更多常规信息，以及工作室所包含的机器学习算法，则可参考下述资源：
>
- [什么是机器学习工作室？](what-is-ml-studio.md) - 对工作室的概述。
- [机器学习基础知识及算法示例](basics-infographic-with-algorithm-examples.md) - 以信息图方式图文并茂地详细介绍了机器学习工作室包含的各类机器学习算法。
- [Machine Learning Guide](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1)（机器学习指南）- 此指南介绍的内容与上面的信息图类似，只是采用了交互的方式。
- [机器学习算法备忘](algorithm-cheat-sheet.md)和[如何选择 Microsoft Azure 机器学习的算法](algorithm-choice.md) - 可供用户下载的海报及深度讨论工作室算法的相关文章。
- [Machine Learning Studio: Algorithm and Module Help](https://msdn.microsoft.com/library/azure/dn905974.aspx)（机器学习工作室：算法和模块帮助）- 包括机器学习算法在内的所有工作室模块的完整参考。

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>机器学习工作室有什么作用？

借助机器学习工作室，可以轻松使用拖放模块（使用预测模型技术预先编写）来设置试验。

用户可通过交互式视觉工作区将***数据集*** 和分析***模块*** 拖放到交互式画布上。 将它们连接到一起就是一个可在机器学习工作室中运行的***试验***。
先***创建模型***，然后***对模型定型***，最后再***对模型进行评分和测试***。

可以反复进行模型设计，不断地编辑和运行试验，直至获得所要的结果。 模型就绪以后，可将其作为 ***Web 服务*** 发布，方便其他用户向其发送新的数据并获得预测结果。

## <a name="open-machine-learning-studio"></a>打开机器学习工作室

若要开始使用工作室，请访问 [https://studio.azureml.net](https://studio.azureml.net)。 如果以前登录过机器学习工作室，请单击“登录”。 否则，请单击“在此注册”并选择免费或付费选项。

![登录到机器学习工作室][sign-in-to-studio]
<br/>
***登录到机器学习工作室***

## <a name="five-steps-to-create-an-experiment"></a>创建试验需执行的五个步骤

在本机器学习教程中，我们按照以下五个基本步骤在机器学习工作室中构建试验，以便对模型进行创建、定型以及评分：

- **创建模型**
    - [步骤 1：获取数据]
    - [步骤 2：准备数据]
    - [步骤 3：定义特征]
- **模型**
    - [步骤 4：选择并应用学习算法]
- **对模型进行评分和测试**
    - [步骤 5：预测新汽车价格]

[步骤 1：获取数据]: #step-1-get-data
[步骤 2：准备数据]: #step-2-prepare-the-data
[步骤 3：定义特征]: #step-3-define-features
[步骤 4：选择并应用学习算法]: #step-4-choose-and-apply-a-learning-algorithm
[步骤 5：预测新汽车价格]: #step-5-predict-new-automobile-prices

> [!TIP] 
> 可在 [Azure AI Gallery](https://gallery.cortanaintelligence.com)（Azure AI 库）中找到以下试验的工作副本。 请访问 **[Your first data science experiment - Automobile price prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)**（第一个数据科学试验 - 汽车价格预测），并单击“在工作室中打开”将试验副本下载到机器学习工作室的工作区。


## <a name="step-1-get-data"></a>步骤 1：获取数据

若要进行机器学习，首先需获取数据。
可以使用机器学习工作室随附的多个示例数据集，也可以从多种源导入数据。 本示例将使用工作区中包含的示例数据集“汽车价格数据(原始)”。
此数据集包含各辆汽车的条目，包括制造商、车型、技术规格、价格等方面的信息。

下面介绍如何将数据集导入试验中。

1. 创建新的试验，方法是：单击“机器学习工作室”窗口底部的“+新建”，选择“试验”，并选择“空白试验”。

2. 试验有一个默认名称，显示在画布顶部。 选中该名称，将试验重命名为某个有意义的名称，例如“汽车价格预测”。 名称不需唯一。

    ![将试验重命名][rename-experiment]

2. 试验画布左侧是数据集和模块的控制板。 在此控制板顶部的“搜索”框中键入**汽车**，找到标有“汽车价格数据(原始)”的数据集。 将该数据集拖放到试验画布上。

    ![找到汽车数据集并将其拖放到试验画布上][type-automobile]
    <br/>
    ***找到汽车数据集并将其拖放到试验画布上***

要查看此数据的大致外观，请单击汽车数据集底部的输出端口，并选择“可视化”。

![单击输出端口，并选择“可视化”][select-visualize]
<br/>
***单击输出端口，并选择“可视化”***

> [!TIP]
> 数据集和模块都有由小圆圈表示的输入和输出端口 - 输入端口位于顶部，输出端口位于底部。
要通过试验创建数据流，需将一个模块的输出端口连接到另一个模块的输入端口。
可以随时单击数据集或模块的输出端口，查看数据流中的数据在该时刻的情况。

在此示例数据集中，一辆汽车显示为一行，与之相关的变量显式为多个列。 有了特定汽车的变量以后，我们就可以尝试预测汽车的价格，如最右列（第 26 列，名为“价格”）所示。

![在数据可视化窗口中查看汽车数据][visualize-auto-data]
<br/>
***在数据可视化窗口中查看汽车数据***

单击右上角的“x”关闭可视化窗口。

## <a name="step-2-prepare-the-data"></a>步骤 2：准备数据

数据集通常需要经过一定的预处理才能进行分析。 例如，可能已注意到，在多个行的列中存在缺失值。 需要清除这些缺失值，使模型能够正确分析数据。 在本例中，将删除包含缺失值的所有行。 此外，“规范化损失”列包含较大比例的缺失值，因此要将该列从模型中完全排除。

> [!TIP]
> 使用大多数模块时，都必须从输入数据中清除缺失值。

首先添加一个彻底删除“规范化损失”列的模块，然后添加另一个删除任何有缺失数据的行的模块。

1. 在模块控制板顶部的“搜索”框中键入“选择列”，查找[选择数据集中的列][select-columns]模块，然后将其拖放到试验画布上。 使用此模块可以选择要将哪些列包含在模型中，或者从模型中排除。

2. 将“汽车价格数据(原始)”数据集的输出端口连接到[选择数据集中的列][select-columns]模块的输入端口。

    ![将“选择数据集中的列”模块添加到试验画布并进行连接][type-select-columns]
    <br/>
    ***将“选择数据集中的列”模块添加到试验画布并进行连接***

3. 单击[选择数据集中的列][select-columns] 模块，并单击“属性”窗格中的“启动列选择器”。

    - 在左侧单击“使用规则”
    - 在“开头为”下面，单击“所有列”。 这会指示[选择数据集中的列][select-columns]传递所有列（但要排除的列除外）。
    - 在下拉列表中，选择“排除”和“列名称”，并在文本框内部单击。 此时会显示列的列表。 选择“规范化损失”，该列随即添加到文本框中。
    - 单击复选标记（“确定”）按钮，关闭列选择器（右下角）。

    ![启动列选择器，排除“规范化损失”列][launch-column-selector]
    <br/>
    ***启动列选择器，排除“规范化损失”列***

    此时“选择数据集中的列”的属性窗格指示它将传入数据集中的所有列，但“规范化损失”除外。

    ![属性窗格显示“规范化损失”列已排除][showing-excluded-column]
    <br/>
    ***属性窗格显示“规范化损失”列已排除***

    > [!TIP]
    可以双击模块并输入文本，为模块添加注释。 这有助于快速查看模块在实验中的运行情况。 在本例中，请双击[选择数据集中的列][select-columns]模块，并键入注释“排除规范化损失”。


    ![双击要添加注释的模块][add-comment]
    <br/>
    ***双击要添加注释的模块***

3. 将[清理缺失数据][clean-missing-data]模块拖到试验画布，然后将其连接到[选择数据集中的列][select-columns]”模块。 在“属性”窗格的“清理模式”下选择“删除整行”。 这会指示[清理缺失数据][clean-missing-data]清理数据，删除存在缺失值的行。 双击该模块并键入注释“删除缺失值行”。

    ![将“清理缺失数据”模块的清理模式设置为“删除整行”][set-remove-entire-row]
    <br/>
    ***将“清理缺失数据”模块的清理模式设置为“删除整行”***

4. 通过单击页面底部的“运行”运行此试验。

    试验运行完以后，所有模块都会出现绿色复选标记，表示已成功完成。 另请留意右上角的“已完成运行”状态。

![运行以后，试验看起来应与上图类似][early-experiment-run]
<br/>
***运行以后，试验看起来应与上图类似***

> [!TIP]
> 为什么我们现在运行此试验？ 运行此试验，数据的列定义就会从数据集传入[选择数据集中的列][select-columns]模块和[清理缺失数据][clean-missing-data]模块。 这意味着，只要连接到[清理缺失数据][clean-missing-data]，任何模块也都会有此类相同信息。

试验到目前为止所完成的工作就是清理数据。 要查看已清理的数据集，请单击[清理缺失数据][clean-missing-data]模块左侧的输出端口，并选择“可视化”。 请注意，此时不再包含“规范化损失”列，并且也没有缺失值。

现已清理数据，接下来可以指定要在预测模型中使用哪些特征。

## <a name="step-3-define-features"></a>步骤 3：定义特征

在机器学习中， *特征* 是用户感兴趣的某些内容的各个可测量属性。 在此处的数据集中，每个行代表一辆汽车，每个列是该汽车的特征。

若要找到一组理想的特征来创建预测模型，需要针对要解决的问题进行试验，并且具有相关知识。 有些特征比其他特征更适合用于预测目标。 另外，某些特征与其他特征有很强的关联性，可以将其删除。 例如，city-mpg（市区油耗）和 highway-mpg（高速公路油耗）密切相关，因此可以保留一个，删除另一个，不会对预测产生明显影响。

让我们构建一个模型，它使用数据集中的一部分特征。 以后还可以返回此处，选择不同的特征，再次运行试验，并确认是否获得了理想的结果。 不过，让我们先尝试使用以下特征：

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. 将另一[选择数据集中的列][select-columns]模块拖放到试验画布上。 将[清理缺失数据][clean-missing-data]模块左侧的输出端口连接到[选择数据集中的列][select-columns]模块的输入端口。

    ![将“选择数据集中的列”模块连接到“清理缺失数据”模块][connect-clean-to-select]
    <br/>
    ***将“选择数据集中的列”模块连接到“清理缺失数据”模块***

2. 双击该模块，并键入“选择要预测的特征”。

2. 单击“属性”窗格中的“启动列选择器”。

3. 单击“使用规则”。

4. 在“开头为”下面，单击“没有列”。 在筛选器行中，选择“包括”和“列名”，并在文本框中选择列名列表。 这会指示模块不要传入任何列（特征），我们指定的列除外。

5. 单击复选标记（“确定”）按钮。

    ![选择要包括在预测中的列（特征）][select-columns-to-include]
    <br/>
    ***选择要包括在预测中的列（特征）***

此时会生成经过筛选的数据集，只包含需要传递到下一步使用的学习算法中的特征。 稍后可以返回，选择不同的特征重试生成结果。

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>步骤 4：选择并应用学习算法

准备好数据后，构造预测模型的过程包括训练和测试。 我们将使用数据对模型定型，然后测试模型，看其预测价格时准确性如何。
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*分类* 和*回归* 是两种监督式机器学习算法。 分类可以从一组定义的类别预测答案，例如颜色（红、蓝或绿）。 回归用于预测数字。

由于要预测价格（一个数字），因此需使用回归算法。 就此示例来说，我们将使用简单的*线性回归* 模型。

> [!TIP]
> 若需详细了解不同类型的机器学习算法和使用时机，可以观看“适合初学者的数据科学”系列的第一个视频：[数据科学回答的五大问题](data-science-for-beginners-the-5-questions-data-science-answers.md)。 也可参阅带信息图的[机器学习基础知识及算法示例](basics-infographic-with-algorithm-examples.md)，或查看[机器学习算法备忘](algorithm-cheat-sheet.md)。

对模型定型时，我们会为其提供一组包含价格的数据。 模型会扫描数据，查找汽车特征与其价格的关联性。 然后，我们会测试模型 - 我们会为模型提供一组熟悉的汽车特征，看模型预测已知价格的准确性如何。

我们会将数据拆分为单独的定型数据集和测试数据集，用于模型定型和测试。

1. 选择[拆分数据][split]模块并将其拖到试验画布，然后将其连接到最后一个[选择数据集中的列][select-columns]模块的输出。

2. 单击[拆分数据][split]模块将其选中。 找到“第一个输出数据集中的行的比例”（位于画布右侧的“属性”窗格中），将其设置为 0.75。 这样，我们将使用 75% 的数据进行模型定型，保留 25% 的数据用于测试（可在以后使用不同的百分比进行试验）。

    ![将“拆分数据”模块的拆分比例设置为 0.75][set-split-data-percentage]
    <br/>
    ***将“拆分数据”模块的拆分比例设置为 0.75***

    > [!TIP]
    > 更改“随机种子”参数可为训练和测试生成不同的随机样本。 此参数控制伪随机数生成器的种子。

2. 运行试验。 运行试验时，[选择数据集中的列][select-columns]和[拆分数据][split]模块会将列定义传递到接下来要添加的模块。  

3. 要选择学习算法，请在画布左侧的模块控制板中展开“机器学习”类别，并展开“初始化模型”。 此时会显示多个可用于初始化机器学习算法的模块类别。 对于本试验，请选择“回归”类别下的[线性回归][linear-regression]模块，然后将其拖放到试验画布上。
（也可以在控制板的“搜索”框中键入“线性回归”找到该模块。）

4. 找到[训练模型][train-model]模块并将其拖到试验画布。 将[线性回归][linear-regression]”模块的输出连接到[定型模型][train-model]模块左侧的输入，将[拆分数据][split]”模块的定型数据输出（左端口）连接到[定型模型][train-model]”模块右侧的输入。

    ![将“定型模型”模块连接到“线性回归”和“拆分数据”模块][connect-train-model]
    <br/>
    ***将“定型模型”模块连接到“线性回归”和“拆分数据”模块***

5. 选择“训练模型[模块][train-model]，单击**属性**窗格中的**启动列选择器**，并选择**价格**列。 这是模型要预测的值。

    在列选择器中选择“价格”列，方法是将其从“可用列”列表移至“所选列”列表。

    ![选择“定型模型”模块的“价格”列][select-price-column]
    <br/>
    ***选择“定型模型”模块的“价格”列***

6. 运行试验。

我们现在获得了一个经过定型的回归模型，用来为新的汽车数据评分，以便进行价格预测。

![运行以后，试验现在看起来应与上图类似][second-experiment-run]
<br/>
***运行以后，试验现在看起来应与上图类似***

## <a name="step-5-predict-new-automobile-prices"></a>步骤 5：预测新汽车价格

使用 75% 的数据训练模型后，可以使用该模型为另外 25% 的数据评分，确定模型的运行情况。

1. 找到[评分模型][score-model]模块并将其拖放到试验画布上。 将[定型模型][train-model]模块的输出连接到[评分模型][score-model]的左侧输入端口。 将[拆分数据][split]模型的测试数据输出（右侧端口）连接到[评分模型][score-model]

    ![将“评分模型”模块连接到“定型模型”和“拆分数据”模块][connect-score-model]
    <br/>
    ***将“评分模型”模块连接到“定型模型”和“拆分数据”模块***

2. 运行试验，查看[评分模型][score-model]”模块的输出（单击[评分模型][score-model]的输出端口，并选择“可视化”）。 输出显示价格预测值，以及来自测试数据的已知值。  

    ![“评分模型”模块的输出][score-model-output]
    <br/>
    ***“评分模型”模块的输出***

3. 最后，我们对结果的质量进行测试。 选择[评估模型][evaluate-model]模块并将其拖放到试验画布上，然后将[评分模型][score-model]模块的输出连接到[评估模型][evaluate-model]的左侧输入。

    > [!TIP]
    > 之所以[评估模型][evaluate-model]模块上有两个输入端口，是因为可将其用于并列比较两个模型。 可在以后向试验添加另一算法，并使用[评估模型][evaluate-model]查看哪一个算法的结果更好。

4. 运行试验。

要查看[评估模型][evaluate-model]模块的输出，请单击输出端口，并选择“可视化”。

![试验的评估结果][evaluation-results]
<br/>
***试验的评估结果***

针对本例中的模型显示了以下统计信息：

- **平均绝对误差** (MAE)：绝对误差的平均值（ *误差* 是指预测值与实际值之间的差异）。
- **均方根误差** (RMSE)：对测试数据集所做预测的平均误差的平方根。
- **相对绝对误差**：相对于实际值与所有实际值平均值之间的绝对差异的绝对误差平均值。
- **相对平方误差**：相对于实际值与所有实际值平均值之间的平方差异的平方误差平均值。
- **决定系数**：也称为 **R 平方值**，这是一个统计度量值，表示模型的数据拟合度。

每个误差统计值越小越好。 值越小，表示预测越接近实际值。 对于 **决定系数**，其值越接近 1 (1.0)，预测就越精确。

## <a name="final-experiment"></a>最终试验

最终试验看起来应与下图类似：

![最终试验][complete-linear-regression-experiment]
<br/>
***最终试验***

## <a name="next-steps"></a>后续步骤

完成第一篇机器学习教程并设置试验后，可以继续改进模型，然后将其作为预测型 Web 服务进行部署。

- **通过反复尝试改进模型** - 例如，可以更改预测时使用的特征。 或者，可以[修改线性回归][linear-regression]算法的属性，或尝试完全不同的算法。 甚至可以同时在试验中添加多个机器学习算法，使用[评估模型][evaluate-model]模块进行比较（两两相比）。
有关如何在单个试验中比较多个模型的示例，请参阅 [Azure AI Gallery](https://gallery.cortanaintelligence.com)（Azure AI 库）中的 [Compare Regressors](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5)（比较回归量）。

    > [!TIP]
    > 若要复制试验的任何迭代，请使用页面底部的“另存为”按钮。 可以单击页面底部的“查看运行历史记录”来查看试验的所有迭代。 有关详细信息，请参阅 [在 Azure 机器学习工作室中管理试验迭代][runhistory]。

[runhistory]: manage-experiment-iterations.md

- **将模型部署为预测型 Web 服务** - 如果对模型感到满意，可将其部署为 Web 服务，以便使用新数据预测汽车价格。 有关详细信息，请参阅[部署 Azure 机器学习 Web 服务][publish]。

[publish]: publish-a-machine-learning-web-service.md

想了解详细信息吗？ 如需更广泛细致地了解如何创建、定型、评分和部署模型，请参阅[使用 Azure 机器学习开发预测解决方案][walkthrough]。

[walkthrough]: walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
