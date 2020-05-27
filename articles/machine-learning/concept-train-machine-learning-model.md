---
title: 构建并训练模型
titleSuffix: Azure Machine Learning
description: 了解可用于在 Azure 机器学习中训练模型的不同方法。 估算器提供了一种简单的方法来使用常见框架，如 Scikit-learn、TensorFlow、Keras、PyTorch 和 Chainer。 机器学习管道使你可以轻松计划无人参与的运行，使用异类计算环境，并重复使用工作流的某些部分。 运行配置则提供了对运行训练过程的计算目标的精细控制。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 593ca5d63245ed664b5f63373d1d651129055544
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592383"
---
# <a name="train-models-with-azure-machine-learning"></a>使用 Azure 机器学习训练模型

Azure 机器学习提供多种方法来训练模型，从使用 SDK 的代码优先解决方案到自动化机器学习和可视化设计器等低代码解决方案。 使用以下列表来确定哪种训练方法适合你：

+ [适用于 Python 的 Azure 机器学习 SDK](#python-sdk)：Python SDK 提供多种方法来训练模型，每个模型都具有不同功能。

    | 训练方法 | 说明 |
    | ----- | ----- |
    | [运行配置](#run-configuration) | 训练模型的一种通用方法是使用训练脚本并运行配置。 运行配置提供了配置用于训练模型的训练环境所需的信息。 可以使用运行配置、训练脚本和计算目标（训练环境），并运行训练作业。 |
    | [自动化机器学习](#automated-machine-learning) | 可以通过自动化机器学习来训练模型，而无需大量数据科学或编程知识。 对于具有数据科学和编程背景的人员，它提供了一种方法，可通过自动化算法选择和超参数优化来节省时间和资源。 使用自动化机器学习时，无需担心定义运行配置。 |
    | [估算器](#estimators) | 利用估算器类，可以轻松地根据常用机器学习框架来训练模型。 有用于 Scikit-learn、PyTorch、TensorFlow、Chainer 和 Ray RLlib 的估算器类。 还有一个泛型估算器，可用于尚无专用估算器类的框架。 使用估算器时，无需担心定义运行配置。 |
    | [机器学习管道](#machine-learning-pipeline) | 管道不是一种不同的训练方法，而是一种使用模块化、可重复使用的步骤定义工作流的方法，该方法可以将训练作为工作流的一部分包含在内。 机器学习管道支持使用自动化机器学习、估算器和运行配置来训练模型。 由于管道并非专门针对训练，因此使用管道的原因比其他训练方法更多样。 通常，可以在以下情况下使用管道：<br>* 你需要计划无人参与的进程，如长时间运行的训练作业或数据准备。<br>* 使用多个步骤跨异类计算资源和存储位置进行协调。<br>* 将管道用作针对特定场景的可重复使用的模板，如重新训练或批处理评分。<br>* 对工作流的数据源、输入和输出进行跟踪和版本控制。<br>* 工作流由不同的团队实现，这些团队单独执行特定步骤。 然后，可以在管道中将步骤联接在一起以实现工作流。 |

+ [适用于 Python 的 Azure 机器学习 SDK](#r-sdk)：SDK 使用网状包绑定到 Azure 机器学习的 Python SDK。 这样，便可以从任何 R 环境访问 Python SDK 中实现的核心对象和方法。

+ 设计器：Azure 机器学习设计器（预览版）为机器学习提供了一个简单的入门点，用于构建概念证明，或者为几乎没有编码经验的用户提供入门指导。 它允许你使用拖放基于 Web 的 UI 来训练模型。 可以使用 Python 代码作为设计的一部分，或在不编写任何代码的情况下训练模型。

+ **CLI**：机器学习 CLI 为使用 Azure 机器学习的常见任务提供命令，通常用于脚本编写和自动化任务。 例如，在创建训练脚本或管道后，你可以使用 CLI 按计划启动训练运行，或在用于训练的数据文件更新时启动。 对于训练模型，它提供了提交训练作业的命令。 它可以使用运行配置或管道来提交作业。

其中每个训练方法均可使用不同类型的计算资源进行训练。 这些资源统称为[计算目标](concept-azure-machine-learning-architecture.md#compute-targets)。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。

## <a name="python-sdk"></a>Python SDK

可以将适用于 Python 的 Azure 机器学习 SDK 与 Azure 机器学习结合使用，来构建和运行机器学习工作流。 可以通过交互式式 Python 会话、Jupyter Notebook、Visual Studio Code 或其他 IDE 与服务进行交互。

* [什么是适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [安装/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [配置 Azure 机器学习的开发环境](how-to-configure-environment.md)

### <a name="run-configuration"></a>运行配置

使用 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 可以定义具有 Azure 机器学习的一般训练作业。 然后使用运行配置，以及训练脚本来训练计算目标上的模型。

可以从本地计算机的运行配置开始，然后根据需要切换到基于云的计算目标。 更改计算目标时，只需更改所使用的运行配置。 运行还会记录有关训练作业的信息，例如输入、输出和日志。

* [什么是运行配置？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教程：训练第一个 ML 模型](tutorial-1st-experiment-sdk-train.md)
* [示例：Jupyter Notebook 训练模型示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [如何：设置计算目标并将其用于模型训练](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>自动化机器学习

定义迭代、超参数设置、特征化和其他设置。 在训练过程中，Azure 机器学习会并行尝试不同的算法和参数。 一旦训练达到你定义的退出条件就会停止。 使用估算器时，无需担心定义运行配置。

> [!TIP]
> 除了 Python SDK 外，还可以通过 [Azure 机器学习工作室](https://ml.azure.com)使用自动化 ML。

* [什么是自动化机器学习？](concept-automated-ml.md)
* [教程：使用自动化机器学习创建第一个分类模型](tutorial-first-experiment-automated-ml.md)
* [教程：使用自动化机器学习预测出租车费](tutorial-auto-train-models.md)
* [示例：Jupyter Notebook 自动化机器学习示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [如何：使用 Python 配置自动化 ML 试验](how-to-configure-auto-train.md)
* [如何：自动训练时序预测模型](how-to-auto-train-forecast.md)
* [如何：通过 Azure 机器学习工作室创建、了解和部署自动化机器学习试验](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>估算器

使用估算器可以轻松使用常用的 ML 框架来训练模型。 如果你使用的是 Scikit-learn、PyTorch、TensorFlow、Chainer 或 Ray RLlib，则应考虑使用估算器进行训练。 还有一个泛型估算器，可用于尚无专用估算器类的框架。 使用估算器时，无需担心定义运行配置。

* [什么是估算器？](concept-azure-machine-learning-architecture.md#estimators)
* [教程：使用 Azure 机器学习通过 MNIST 数据和 scikit-learn 训练映像分类模型](tutorial-train-models-with-aml.md)
* [示例：Jupyter Notebook 使用估算器示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [如何：在训练期间创建估算器](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>机器学习管道

机器学习管道可以使用前面提到的训练方法（运行配置、估算器和自动化机器学习）。 管道更多的是关于创建工作流，因此它们包含的不仅仅是模型训练。 在管道中，可以使用自动化机器学习、估算器或运行配置来训练模型。

* [什么是 Azure 机器学习 ML 管道？](concept-ml-pipelines.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)
* [教程：使用 Azure 机器学习管道进行批处理评分](tutorial-pipeline-batch-scoring-classification.md)
* [示例：Jupyter Notebook 机器学习管道示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [示例：使用自动化机器学习的管道](https://aka.ms/pl-automl)
* [示例：使用估算器的管道](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

R SDK 使你能够将 R 语言与 Azure 机器学习结合使用。 SDK 使用网状包绑定到 Azure 机器学习的 Python SDK。 这样，便可以从任何 R 环境访问 Python SDK 中实现的核心对象和方法。

有关详细信息，请参阅以下文章：

* [教程：创建逻辑回归模型](tutorial-1st-r-experiment.md)
* [R 参考的 Azure 机器学习 SDK](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure 机器学习设计器

使用设计器，你可以在 Web 浏览器中使用拖放界面来训练模型。

+ [什么是设计器？](concept-designer.md)
+ [教程：预测汽车价格](tutorial-designer-automobile-price-train-score.md)
+ [回归：预测价格](how-to-designer-sample-regression-automobile-price-basic.md)
+ [分类：预测收入](how-to-designer-sample-classification-predict-income.md)
+ [分类：预测客户流失、购买欲和追加销售](how-to-designer-sample-classification-churn.md)
+ [使用自定义 R 脚本的分类：预测航班延误](how-to-designer-sample-classification-flight-delay.md)
+ [文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

机器学习 CLI 是 Azure CLI 的扩展。 它为使用 Azure 机器学习提供了跨平台 CLI 命令。 通常，可以使用 CLI 来自动执行任务，如训练机器学习模型。

* [将 CLI 扩展用于 Azure 机器学习](reference-azure-machine-learning-cli.md)
* [Azure 上的 MLOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>后续步骤

了解如何[设置训练环境](how-to-set-up-training-targets.md)。
