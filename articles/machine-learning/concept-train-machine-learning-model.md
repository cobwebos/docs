---
title: 模型定型方法
titleSuffix: Azure Machine Learning
description: 了解可用于在 Azure 机器学习中训练模型的不同方法。 估算提供了一种简单的方法来使用常见框架，如 Scikit-learn、TensorFlow、Keras、PyTorch 和 Chainer。 机器学习管道使你可以轻松地计划无人参与的运行，使用异类计算环境，并重复使用工作流的某些部分。 和运行配置提供对运行定型过程的计算目标的精细控制。
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: f46dd2b30ca84a7e6a1b0fc34ef0fa5bafffaef5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721109"
---
# <a name="train-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 학습

Azure 机器学习提供多种方法来训练模型，从使用 SDK 的代码优先解决方案到低代码解决方案（例如自动机器学习和可视化设计器）。 使用以下列表来确定哪种培训方法适合你：

+ [适用于 python 的 AZURE 机器学习 SDK](#python-sdk)： python sdk 提供多种方法来训练模型，每个模型都具有不同的功能。

    | 定型方法 | Description |
    | ----- | ----- |
    | [运行配置](#run-configuration) | **训练模型的一般方法**是使用训练脚本并运行配置。 运行配置提供了配置用于定型模型的定型环境所需的信息。 您可以执行运行配置、训练脚本和计算目标（定型环境）并运行培训作业。 |
    | [自动机器学习](#automated-machine-learning) | 通过自动化机器学习，**无需大量数据科学或编程知识即可定型模型**。 对于具有数据科学和编程背景的人员，它提供了一种方法，可通过自动化算法选择和超参数优化来节省时间和资源。 使用自动机器学习时，无需担心定义运行配置。 |
    | [估算](#estimators) | 利用估计器类，**可以轻松地根据常用机器学习框架来训练模型**。 **Scikit-learn**、 **PyTorch**、 **TensorFlow**和**Chainer**有估计器类。 还有一个泛型估计器，可用于尚无专用估计器类的框架。 使用估算时，无需担心定义运行配置。 |
    | [机器学习管道](#machine-learning-pipeline) | 管道并不是一种不同的定型方法，而是**使用模块化的可重用步骤定义工作流的一种方法**，该方法可以包含定型作为工作流的一部分。 机器学习管道支持使用自动机器学习、估算和运行配置来训练模型。 由于管道并非专门针对定型，因此使用管道的原因比其他培训方法更有不同。 通常情况下，可以在以下情况下使用管道：<br>* 你需要**计划无人参与的进程**，如长时间运行的培训作业或数据准备。<br>* 使用跨异类计算资源和存储位置协调的**多个步骤**。<br>* 将管道用作特定方案（如重新训练或批处理评分）的**可重用模板**。<br>* **跟踪和版本工作流的数据源、输入和输出**。<br>* 你的工作流**由单独处理特定步骤的不同团队实现**。 然后，可以在管道中将步骤联接在一起以实现工作流。 |

+ **设计器**： Azure 机器学习设计器（预览）提供了一个简单的入口点，可用于构建概念证明或几乎编码经验的用户。 它允许您使用拖放基于 web 的 UI 来训练模型。 您可以使用 Python 代码作为设计的一部分，或在不编写任何代码的情况下定型模型。

+ **CLI**：机器学习 CLI 为使用 Azure 机器学习的常见任务提供命令，通常用于**脚本和自动化任务**。 例如，在创建训练脚本或管道后，您可以使用 CLI 按计划启动定型运行，或在用于定型的数据文件更新时启动。 对于定型模型，它提供了用于提交训练作业的命令。 它可以使用运行配置或管道提交作业。

其中每个定型方法均可使用不同类型的计算资源进行定型。 这些资源统称为[__计算目标__](concept-azure-machine-learning-architecture.md#compute-targets)。 计算目标可以是本地计算机或云资源，如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。

## <a name="python-sdk"></a>Python SDK

用于 Python 的 Azure 机器学习 SDK 允许您通过 Azure 机器学习生成并运行机器学习工作流。 可以通过交互式 Python 会话、Jupyter 笔记本、Visual Studio Code 或其他 IDE 与服务进行交互。

* [用于 Python 的 Azure 机器学习 SDK 是什么](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [安装/更新 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [为 Azure 机器学习配置开发环境](how-to-configure-environment.md)

### <a name="run-configuration"></a>실행 구성

可以使用[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)定义具有 Azure 机器学习的一般训练作业。 然后，将使用运行配置，以及训练脚本来训练计算目标上的模型。

你可以从本地计算机的运行配置开始，然后根据需要切换到基于云的计算目标。 更改计算目标时，只更改所使用的运行配置。 运行还会记录有关训练作业的信息，例如输入、输出和日志。

* [什么是运行配置？](concept-azure-machine-learning-architecture.md#run-configurations)
* [教程：训练首个 ML 模型](tutorial-1st-experiment-sdk-train.md)
* [示例：定型模型的 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [如何：设置和使用模型定型的计算目标](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

定义迭代、超参数设置、特征化和其他设置。 在训练过程中，Azure 机器学习会并行尝试不同的算法和参数。 训练一旦达到你定义的出口标准就会停止。 使用估算时，无需担心定义运行配置。

> [!TIP]
> 除了 Python SDK 外，还可以通过[Azure 机器学习 studio](https://ml.azure.com)使用自动 ML。

* [什么是自动化机器学习？](concept-automated-ml.md)
* [教程：通过自动机器学习创建您的第一个分类模型](tutorial-first-experiment-automated-ml.md)
* [教程：使用自动机器学习预测出租车费用](tutorial-auto-train-models.md)
* [示例：自动机器学习 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [如何：在 Python 中配置自动 ML 试验](how-to-configure-auto-train.md)
* [如何： Autotrain 时序预测模型](how-to-auto-train-forecast.md)
* [如何：通过[Azure 机器学习 Studio](how-to-create-portal-experiments.md)创建、探索和部署自动化机器学习试验

### <a name="estimators"></a>估算

使用估算可以轻松地使用常用 ML 框架训练模型。 如果你使用**的是 scikit-learn**、 **PyTorch**、 **TensorFlow**或**Chainer**，你应考虑使用估计器进行培训。 还有一个泛型估计器，可用于尚无专用估计器类的框架。 使用估算时，无需担心定义运行配置。

* [什么是估算？](concept-azure-machine-learning-architecture.md#estimators)
* [教程：使用 MNIST 数据和 scikit-learn 为图像分类模型定型-了解 Azure 机器学习](tutorial-train-models-with-aml.md)
* [示例：使用估算 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [如何：在定型中创建估算](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>机器学习管道

机器学习管道可以使用前面提到的培训方法（运行配置、估算和自动化机器学习）。 管道是有关创建工作流的详细信息，因此它们不仅包含模型的定型。 在管道中，可以使用自动机器学习、估算或运行配置来训练模型。

* [什么是 ML 管道 Azure 机器学习？](concept-ml-pipelines.md)
* [Azure 机器学习 SDK 中创建和运行机器学习管道](how-to-create-your-first-pipeline.md)
* [教程：将 Azure 机器学习管道用于批处理评分](tutorial-pipeline-batch-scoring-classification.md)
* [示例：机器学习管道 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [示例：具有自动机器学习的管道](https://aka.ms/pl-automl)
* [示例：具有估算的管道](https://aka.ms/pl-estimator)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning 디자이너

设计器使您能够在 web 浏览器中使用拖放界面训练模型。

+ [什么是设计器？](concept-designer.md)
+ [教程：预测汽车价格](tutorial-designer-automobile-price-train-score.md)
+ [回归：预测价格](how-to-designer-sample-regression-automobile-price-basic.md)
+ [分类：预测收入](how-to-designer-sample-classification-predict-income.md)
+ [分类：预测变动率、亲和力和向上销售](how-to-designer-sample-classification-churn.md)
+ [带有自定义 R 脚本的分类：预测航班延迟](how-to-designer-sample-classification-flight-delay.md)
+ [文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>CLI

机器学习 CLI 是 Azure CLI 的扩展。 它提供了用于处理 Azure 机器学习的跨平台 CLI 命令。 通常，使用 CLI 来自动执行任务，如训练机器学习模型。

* [使用 CLI 扩展进行 Azure 机器学习](reference-azure-machine-learning-cli.md)
* [Azure 上的 MLOps](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>다음 단계

了解如何[设置培训环境](how-to-set-up-training-targets.md)。
