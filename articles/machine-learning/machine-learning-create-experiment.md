<properties
	pageTitle="机器学习工作室中的简单试验 | Microsoft Azure"
	description="本机器学习教程逐步讲解如何轻松完成数据科研试验。使用回归算法预测汽车价格。"
	keywords="试验, 线性回归, 机器学习算法, 机器学习教程, 预测建模技术, 数据科研试验"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="garye"/>

# 机器学习教程：在 Azure 机器学习工作室中创建第一个数据科研试验

本机器学习教程逐步讲解如何轻松完成数据科研试验。我们将创建一个线性回归模型，使用制造和技术规格等各种变量来预测汽车的价格。为此，我们将使用 Azure 机器学习工作室来开发和迭代一个简单的预测分析试验。

*预测分析*是使用当前数据预测未来结果的数据科研技术。有关预测分析的简单示例，请观看适用于初学者的数据科研视频 4：[Predict an answer with a simple model](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md)（使用简单模型预测答案）（运行时长：7:42）。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 机器学习工作室有什么作用？

借助机器学习工作室，可以轻松使用拖放模块（使用预测模型技术预先编写）来设置试验。若要运行试验和预测答案，可以使用机器学习工作室*创建模型*、*训练模型*以及*对模型进行评分与测试*。

进入机器学习工作室：[https://studio.azureml.net](https://studio.azureml.net)。如果以前登录过机器学习工作室，请单击“在此登录”。否则，请单击“注册”并选择免费或付费选项。

有关机器学习工作室的一般信息，请参阅 [What is Machine Learning Studio?](machine-learning-what-is-ml-studio.md)（什么是机器学习工作室？）

## 创建试验需执行的五个步骤

在本机器学习教程中，可以遵循以下五个基本步骤在机器学习工作室中构建试验，用于创建、训练模型并为其评分：

- 创建模型
	- [步骤 1：获取数据]
	- [步骤 2：预处理数据]
	- [步骤 3：定义特征]
- 训练模型
	- [步骤 4：选择并应用学习算法]
- 对模型进行评分和测试
	- [步骤 5：预测新汽车价格]

[步骤 1：获取数据]: #step-1-get-data
[步骤 2：预处理数据]: #step-2-preprocess-data
[步骤 3：定义特征]: #step-3-define-features
[步骤 4：选择并应用学习算法]: #step-4-choose-and-apply-a-learning-algorithm
[步骤 5：预测新汽车价格]: #step-5-predict-new-automobile-prices


## 步骤 1：获取数据

机器学习工作室随附多个示例数据集供选择，可以从许多源导入数据。本示例将使用随附的示例数据集“汽车价格数据(原始)”。此数据集包含多种不同汽车的项，包括制造、模型、技术规格和价格等信息。

1. 单击“机器学习工作室”窗口底部的“+新建”，选择“试验”，然后选择“空白试验”开始新的试验。选择画布顶部的默认试验名称，然后将它重命名为有意义的名称，例如“汽车价格预测”。

2. 试验画布左侧是数据集和模块的控制板。在此控制板顶部的“搜索”框中键入**汽车**，找到标有“汽车价格数据(原始)”的数据集。

	![控制板搜索][screen1a]

3. 将该数据集拖到试验画布中。

	![数据集][screen1]

若要查看此数据的大致外观，请单击汽车数据集底部的输出端口，然后选择“可视化”。

![模块输出端口][screen1c]

数据集中的变量显示为列，汽车的每个实例显示为行。最右侧的列（列 26，标题为“价格”）是要尝试预测的目标变量。

![数据集可视化][screen1b]

单击右上角的“x”关闭可视化窗口。

## 步骤 2：预处理数据

数据集通常需要经过一定的预处理才能进行分析。可能已注意到在各个行的列中存在缺失值。需要清除这些缺失值，使模型能够正确分析数据。在本例中，将删除包含缺失值的所有行。此外，“规范化损失”列包含较大比例的缺失值，因此要将该列从模型中完全排除。

> [AZURE.TIP] 使用大多数模块时，都必须从输入数据中清除缺失值。

首先删除“规范化损失”列，然后删除所有包含缺失数据的行。

1. 在模块控制板顶部的“搜索”框中键入**选择列**，查找“选择数据集中的列”模块，然后将其拖到试验画布，并连接到“汽车价格数据(原始)”数据集的输出端口。[][select-columns]使用此模块可以选择要将哪些列包含在模型中，或者从模型中排除。

2. 选择“选择数据集中的列”模块，然后单击“属性”窗格中的“启动列选择器”。[][select-columns]

	- 在左侧单击“使用规则”。
	- 在“开头为”下面，单击“所有列”。这会指示“选择数据集中的列”传递所有列（但要排除的列除外）。[][select-columns]
	- 在下拉列表中，选择“排除”和“列名称”，然后在文本框内部单击。此时将显示列的列表。选择“规范化损失”，该列随即添加到文本框中。
	- 单击复选标记（“确定”）按钮，关闭列选择器。

    ![选择列][screen3]

	“选择数据集中的列”的属性窗格指示它将传递数据集中的所有列，但“规范化损失”除外。

    ![在“数据集”属性中选择列][screen4]

    > [AZURE.TIP] 可以双击模块并输入文本，为模块添加注释。这有助于快速查看模块在试验中的运行情况。在本例中，请双击“选择数据集中的列”模块，然后输入注释“排除规范化损失”。[][select-columns]

3. 将“清理缺失数据”模块拖到试验画布，然后将其连接到“选择数据集中的列”模块。[][clean-missing-data][][select-columns]在“属性”窗格中，以“清理模式”选择“删除整个行”，通过删除包含缺失值的行来清理数据。双击该模块并键入注释“删除缺失值行”。

	![“清理缺失数据”属性][screen4a]

4. 单击试验画布下面的“运行”以运行试验。

试验完成时，所有模块将出现绿色复选标记，表示它们已成功完成。另请留意右上角的“已完成运行”状态。

![第一个试验运行][screen5]

试验到目前为止所完成的工作就是清理数据。若要查看已清理的数据集，请单击“清理缺失数据”模块（“清理的数据集”）左侧的输出端口，然后选择“可视化”。[][clean-missing-data]请注意，此时不再包含“规范化损失”列，并且也没有缺失值。

现已清理数据，接下来可以指定要在预测模型中使用哪些特征。

## 步骤 3：定义特征

在机器学习中，*特征*是用户感兴趣的某些内容的各个可测量属性。在此处的数据集中，每个行代表一辆汽车，每个列是该汽车的特征。

若要找到一组理想的特征来创建预测模型，需要针对要解决的问题进行试验，并且具有相关知识。有些特征比其他特征更适合用于预测目标。此外，有些特征与其他特征密切相关（例如 city-mpg 与 highway-mpg），因此不会在模型中添加太多的新信息，并且可以删除这些特征。

让我们构建一个模型，它使用数据集中的一部分特征。可以返回选择不同的特征，再次运行试验，然后确认是否获得了理想的结果。不过，让我们先尝试使用以下特征：

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. 将另一个“选择数据集中的列”模块拖到试验画布，并将它连接到“清除缺失数据”模块左侧的输出端口。[][select-columns][][clean-missing-data]双击该模块，然后键入“选择要预测的特征”。

2. 单击“属性”窗格中的“启动列选择器”。

3. 单击“使用规则”。

4. 在“开头为”下面，单击“无列”，然后在筛选器行中选择“包含”和“列名称”。输入列名称的列表。这会指示模块仅传递指定的列。

	> [AZURE.TIP] 运行试验可确保数据的列定义从数据集传入“清除缺失数据”模块。[][clean-missing-data]这意味着，连接的其他模块也包含数据集中的信息。

5. 单击复选标记（“确定”）按钮。

![选择列][screen6]

这样就生成了要在后续步骤所述的学习算法中使用的数据集。稍后可以返回，选择不同的特征重试生成结果。

## 步骤 4：选择并应用学习算法

准备好数据后，构造预测模型的过程包括训练和测试。我们将使用数据来训练模型，然后测试模型，确定它如何精确预测价格。暂时，请不要考虑为何需要训练然后测试模型。

*分类*和*回归*是两种受监督的机器学习技术。分类可以从一组定义的类别预测答案，例如颜色（红、蓝或绿）。回归用于预测数字。

由于要预测价格（一个数字），因此我们将使用回归模型。在本示例中，我们将训练一个简单的*线性回归*模型，然后在下一步骤中进行测试。

1. 我们将数据拆分为不同的训练集和测试集，以便将这些数据用于训练和测试。选择“拆分数据”模块并将其拖到试验画布，然后将其连接到最后一个“选择数据集中的列”模块的输出。[][split][][select-columns]将“第一个输出数据集中的行部分”设置为 0.75。这样，我们将使用 75% 的数据来训练模型，保留 25% 的数据用于测试。

	> [AZURE.TIP] 更改“随机种子”参数可为训练和测试生成不同的随机样本。此参数控制伪随机数生成器的种子。

2. 运行试验。“选择数据集中的列”和“拆分数据”模块会将列定义传递到接下来要添加的模块。[][select-columns][][split]

3. 若要选择学习算法，请在画布左侧的模块控制板中展开“机器学习”类别，然后展开“初始化模型”。此时将显示多个可用于初始化机器学习算法的模块类别。

	对于本试验，请选择“回归”类别下的“线性回归”模块（也可以在控制板搜索框中键入“线性回归”找到该模块），然后将其拖到试验画布。[][linear-regression]

4. 找到“训练模型”模块并将其拖到试验画布。[][train-model]将左侧的输入端口连接到“线性回归”模块的输出。[][linear-regression]将右侧的输入端口连接到“拆分数据”模块的训练数据输出（左侧端口）。[][split]

5. 选择“训练模型”模块，单击“属性”窗格中的“启动列选择器”，然后选择“价格”列。[][train-model]这是模型要预测的值。

	![选择“价格”列][screen7]

6. 运行试验。

结果是经过训练的回归模型，可用来为新样本评分以进行预测。

![应用机器学习算法][screen8]

## 步骤 5：预测新汽车价格

使用 75% 的数据训练模型后，可以使用该模型为另外 25% 的数据评分，确定模型的运行情况。

1. 找到“评分模型”模块并将其拖到试验画布，然后将左侧的输入端口连接到“训练模型”模块的输出。[][score-model][][train-model]将右侧的输入端口连接到“拆分数据”模块的测试数据输出（右侧端口）。[][split]

	![“评分模型”模块][screen8a]

2. 若要运行试验和查看“评分模型”模块的输出，请单击输出端口，然后选择“可视化”。[][score-model]输出显示价格预测值，以及来自测试数据的已知值。

3. 最后，若要测试结果的质量，请选择“评估模型”模块并将其拖到试验画布，然后将左侧的输入端口连接到“评分模型”模块的输出。[][evaluate-model][][score-model]（之所以有两个输入端口，是因为“评估模型”模块可用于比较两个模型）。[][evaluate-model]

4. 运行试验。

若要查看“评估模型”模块的输出，请单击输出端口，然后选择“可视化”。[][evaluate-model]针对本例中的模型显示了以下统计信息：

- **平均绝对误差** (MAE)：绝对误差的平均值（*误差*是指预测值与实际值之间的差异）。
- **均方根误差** (RMSE)：对测试数据集所做预测的平均误差的平方根。
- **相对绝对误差**：相对于实际值与所有实际值平均值之间的绝对差异的绝对误差平均值。
- **相对平方误差**：相对于实际值与所有实际值平均值之间的平方差异的平方误差平均值。
- **决定系数**：也称为 **R 平方值**，这是一个统计度量值，表示模型的数据拟合度。

每个误差统计值越小越好。值越小，表示预测越接近实际值。对于**决定系数**，其值越接近 1 (1.0)，预测就越精确。

![评估结果][screen9]

最终试验应如下所示：

![机器学习教程：完成使用预测建模技术的线性回归试验。][screen10]

## 后续步骤

完成第一篇机器学习教程并设置试验后，可以反复尝试改善模型。例如，可以更改预测中使用的特征。或者，可以修改[线性回归][linear-regression]算法的属性，或尝试完全不同的算法。甚至可以同时在试验中添加多个机器学习算法，使用“评估模型”模块进行比较（两两相比）。[][evaluate-model]

> [AZURE.TIP] 使用试验画布下面的“另存为”按钮可以复制试验的任何迭代。可以单击画布下面的“查看运行历史记录”来查看试验的所有迭代。有关详细信息，请参阅 [Manage experiment iterations in Azure Machine Learning Studio][runhistory]（在 Azure 机器学习工作室中管理试验迭代）。

[runhistory]: machine-learning-manage-experiment-iterations.md

如果对模型感到满意，可将其部署为 Web 服务，以便使用新数据预测汽车价格。有关详细信息，请参阅 [Deploy an Azure Machine Learning web service][publish]（部署 Azure 机器学习 Web 服务）。

[publish]: machine-learning-publish-a-machine-learning-web-service.md

有关用于创建、训练、评分和部署模型的预测建模技术的更广泛、更详细演练，请参阅 [Develop a predictive solution by using Azure Machine Learning][walkthrough]（使用 Azure 机器学习开发预测解决方案）。

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0921_2016-->