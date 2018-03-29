---
title: 什么是 Azure 机器学习工作室？ | Microsoft Docs
description: 概述 Azure 机器学习工作室：一个拖放式工具，可以通过现成可用的算法库和模块快速构建模型。
keywords: azure 机器学习,azure ml, 机器学习工作室
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.openlocfilehash: e7fb545b985968b4d9e5a516c812cbf594352e08
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="what-is-azure-machine-learning-studio"></a>什么是 Azure 机器学习工作室？
Microsoft Azure 机器学习工作室是一个协作型拖放式工具，可用于根据数据构建、测试和部署预测分析解决方案。 机器学习工作室将模型发布为可让自定义应用或 BI 工具（如 Excel）方便使用的 Web 服务。

机器学习工作室是展开数据科研、执行预测分析、使用云资源和处理数据的中心位置。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>机器学习工作室交互式工作区
要开发预测分析模型，通常需要使用一个或多个源中的数据，通过各种数据操作和统计函数来转换及分析数据，并生成一组结果。 开发此类模型是一个迭代过程。 在修改各种函数及其参数时，结果会不断收敛，直到已训练的有效模型令人满意。

**Azure 机器学习工作室** 提供交互式的可视工作区，可在其中轻松构建、测试和迭代预测分析模型。 可以将***数据集***和分析***模块***拖放到交互式画布，将它们连接在一起构成***试验***，然后在机器学习工作室中运行。 要迭代模型设计，可以编辑试验，根据需要保存副本，并重新运行试验。 准备就绪后，可以将***训练实验***转换为***预测试验***，然后将其发布为 ***Web 服务***，使其他人可以访问模型。

不需要编程，只需以可视方式连接数据集和模块，即可构造预测分析模型。

> [!TIP]
> 若要下载和打印 Microsoft Azure 机器学习工作室功能概述示意图，请参阅 [Overview diagram of Azure Machine Learning Studio capabilities](studio-overview-diagram.md)（Azure Machine 机器学习工作室功能概述）。
> 
> 

![Azure 机器学习工作室示意图：创建试验、读取多个源的数据、写入评分的数据、写入模型。][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>机器学习工作室入门
首次进入[机器学习工作室](https://studio.azureml.net)时，会看到“主页”。 可在此处查看文档、视频、网络研讨会，以及查找其他重要资源。

单击左上方的菜单 ![菜单](./media/what-is-ml-studio/menu.png) 将看到多个选项。

### <a name="cortana-intelligence"></a>Cortana Intelligence
单击“Cortana Intelligence”，会转到 [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite) 的主页。 Cortana Intelligence Suite 是一个完全托管的大数据和高级分析套件，用于将数据转换为智能操作。 有关完整文档（包括客户案例分享），请参阅套件主页。

### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室
此处有两个选项：**主页**（开始的页面）和**工作室**。

单击“工作室”，会转到 **Azure 机器学习工作室**。 首先系统会要求使用 Microsoft 帐户或者工作或学校帐户登录。 登录后，左侧会显示以下选项卡：

* **项目** - 表示单个项目的试验、数据集、笔记本和其他资源的集合
* **试验** - 已创建、运行和保存为草稿的试验
* **WEB 服务** - 已从试验部署的 Web 服务
* **笔记本** - 创建的 Jupyter 笔记本
* **数据集** - 已上载到工作室的数据集
* **训练模型** - 已在试验中训练并保存在工作室中的模型
* **设置** - 用于配置帐户和资源的设置集合。

### <a name="gallery"></a>库
单击“库”就会转到 [Azure AI 库](http://gallery.cortanaintelligence.com/)。 社区数据科研人员和开发人员可以在该库中共享使用 Cortana Intelligence Suite 组件创建的解决方案。

有关该库的详细信息，请参阅[共享和发现 Azure AI 库中的解决方案](gallery-how-to-use-contribute-publish.md)。

## <a name="components-of-an-experiment"></a>试验组件
试验由数据集组成，数据集将数据提供给分析模块，将这些模块连接起来即可构成预测分析模型。 具体而言，有效的试验有以下特征：

* 试验至少包含一个数据集和一个模块
* 数据集只能连接到模块
* 模块可以连接到数据集或其他模块
* 模块的所有输入端口必须与数据流建立某种连接
* 必须设置每个模块的所有必需参数

可以从头开始创建试验，或者使用现有的示例试验作为模板。 有关详细信息，请参阅[复制示例试验以创建新的机器学习试验](sample-experiments.md)。

有关创建简单试验的示例，请参阅 [Create a simple experiment in Azure Machine Learning Studio](create-experiment.md)（在 Azure 机器学习工作室中创建简单试验）。

有关创建预测分析解决方案的更完整演练，请参阅 [Develop a predictive solution with Azure Machine Learning](walkthrough-develop-predictive-solution.md)（使用 Azure 机器学习开发预测解决方案）。

### <a name="datasets"></a>数据集
数据集是指已上传到机器学习工作室，可在建模过程中使用的数据。 机器学习工作室随附了许多示例数据集供试验，可以根据需要上传更多的数据集。 下面是随附数据集的一些例子：

* **各种汽车的 MPG 数据** - 汽车的每加仑燃油英里数 (MPG) 值，按缸数、马力等参数列出。
* **乳腺症数据** - 乳腺癌诊断数据。
* **森林火灾数据** - 葡萄牙东北部森林火灾级别。

构建试验时，可以从画布左侧的数据集列表中选择。

有关机器学习工作室随附的示例数据集列表，请参阅 [Use the sample data sets in Azure Machine Learning Studio](use-sample-datasets.md)（使用 Azure 机器学习工作室中的示例数据集）。

### <a name="modules"></a>模块
模块是可对数据执行的算法。 机器学习工作室有许多模块，包括数据引入函数、训练、评分和验证过程。 下面是随附模块的一些例子：

* [转换为 ARFF][convert-to-arff] - 将 .NET 序列化数据集转换为属性关系文件格式 (ARFF)。
* [计算基本统计信息][elementary-statistics] - 计算基本统计信息，例如平均值、标准偏差等。
* [线性回归][linear-regression] - 创建在线梯度下降线性回归模型。
* [评分模型][score-model] - 为训练的分类或回归模型评分。

构建试验时，可以从画布左侧的模块列表中选择。  

模块可能提供一组参数用于配置模块的内部算法。 在画布上选择模块时，模块的参数会显示在画布右侧的“属性”窗格中。 可以在该窗格中修改参数来调整模型。

在浏览可用的机器学习算法大型库时如需帮助，请参阅 [How to choose algorithms for Microsoft Azure Machine Learning](algorithm-choice.md)（如何选择 Microsoft Azure 机器学习的算法）。

## <a name="deploying-a-predictive-analytics-web-service"></a>部署预测分析 Web 服务
准备好预测分析模型后，可以从机器学习工作室将它部署为 Web 服务。 有关此过程的详细信息，请参阅 [Deploy an Azure Machine Learning web service](publish-a-machine-learning-web-service.md)（部署 Azure 机器学习 Web 服务）。

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg



## <a name="key-machine-learning-terms-and-concepts"></a>机器学习的重要术语和概念
机器学习的术语有点难懂。 下面提供了重要术语的定义。 可以使用下面的“意见”部分告诉我们还要阐释哪些术语。

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>数据探索、描述性分析和预测分析

**数据探索** 是收集大量的、通常是非结构化的数据集的相关信息，找到要重点分析的特征的过程。

**数据挖掘** 指自动化的数据探索。

**描述性分析** 是分析数据集以汇总事件的过程。 大多数商业分析（例如销售报表、Web 指标和社交网络分析）都具有描述性。

**预测分析** 是从历史数据或当前数据构建模型，以预测将来结果的过程。

### <a name="supervised-and-unsupervised-learning"></a>监督式和非监督式学习
 **监督式学习** 算法使用带有标签的数据（即，包含所需示例答案的数据）进行训练。 例如，识别信用卡盗用的模型，是通过包含已知诈骗收费和有效收费标记数据点的数据集训练的。 大多数机器学习都是监督式的。

 **非监督式学习** 用于不带标签的数据，目标是查找数据中的关系。 例如，可以查找具有类似消费习惯的客户人口统计信息。

### <a name="model-training-and-evaluation"></a>模型训练和评估
机器学习模型将尝试回答的问题或要预测的结果抽象化。 模型是通过现有数据训练和评估的。

#### <a name="training-data"></a>训练数据
通过数据训练模型时，将使用已知数据集，基于数据特征进行模型调整，以求获得最准确的结果。 在 Azure 机器学习中，模型是通过处理训练数据和功能模块（例如评分模块）的算法模块构建的。

在监督式学习中，如果要训练诈骗检测模型，可以使用一组标记为诈骗或有效的交易。 随机拆分数据集，并使用一部分数据训练模型，使用另一部分测试或评估模型。

#### <a name="evaluation-data"></a>评估数据
训练模型后，使用剩余测试数据来评估模型。 使用已经知道结果的数据，判断模型的预测是否准确。

## <a name="other-common-machine-learning-terms"></a>其他常见的机器学习术语
* **算法**：通过数据处理、数学计算或自动推理来解决问题的一组独立规则。
* **异常检测**：标记异常的事件或值，帮助发现问题的模型。 例如，信用卡诈骗检测可以查找异常的购买活动。
* **分类数据**：按类别组织的、可分组的数据。 例如，汽车的分类数据集可以指定年份、制造商、车型和价格。
* **分类**：根据类别分组已知的数据集，将数据点组织成不同类别的模型。
* **特征工程**：提取或选择与数据集相关的特征，以便增强数据集并改善结果的过程。 例如，机票价格数据可通过星期几和假日来增强。 请参阅 [Feature selection and engineering in Azure Machine Learning](../team-data-science-process/create-features.md)（Azure 机器学习中的特征选择和工程）。
* **模块**：机器学习工作室模型中的功能组件，例如，用于输入和编辑小型数据集的“输入数据”模块。 算法也是机器学习工作室中的一种模块。
* **模型**：监督式学习模型是机器学习试验的产物，其中包含训练数据、算法模块，以及功能模块（例如“评分模型”模块）。
* **数值数据**：表示度量值（连续数据）或计数（离散数据）的数据。 也名为 *量化数据*。
* **分区**：将数据拆分成多个样本的方法。 有关详细信息，请参阅 [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx) （分区和采样）。
* **预测**：预测是来自机器学习模型的一个或多个预测值。 有时称为“预测评分”。 但是，预测评分不是模型的最终输出。 评分之后还要评估模型。
* **回归**：根据独立变量预测值的模型，例如根据汽车的年份和制造商预测汽车价格。
* **评分**：使用机器学习工作室中的 [“评分模型”模块](https://msdn.microsoft.com/library/azure/dn905995.aspx) ，从训练分类或回归模型生成的预测值。 分类模型也返回预测值的机率评分。 从模型生成分数之后，即可使用 [“评估模型”模块](https://msdn.microsoft.com/library/azure/dn905915.aspx)来评估模型的准确性。
* **样本**：用于代表整体的一部分数据集。 可以随机采样，或根据数据集的具体特征采样。

## <a name="next-steps"></a>后续步骤
可以使用[分步指南](create-experiment.md)和[基于样本的构建](sample-experiments.md)了解预测分析和机器学习的基础知识。  


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
