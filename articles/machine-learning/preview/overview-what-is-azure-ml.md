---
title: 什么是 Azure 机器学习？ | Microsoft Docs
description: 概述 Azure 机器学习试验和模型管理 - 这是一个集成式的端到端数据科学解决方案，可让专业数据科学家以云的规模开发、试验和部署高级分析应用程序。
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: e5716e3fc519c48aaea3ec17939d11008a1b1fd4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-machine-learning"></a>什么是 Azure 机器学习？

Azure 机器学习是集成式的端到端数据科学和高级分析解决方案。 它可让数据科学家以云的规模准备数据、开发试验和部署模型。

Azure 机器学习的主要组件包括：
- Azure Machine Learning Workbench
- Azure 机器学习试验服务
- Azure 机器学习模型管理服务
- 用于 Apache Spark 的 Microsoft 机器学习库（MMLSpark 库）
- 用于 AI 的 Visual Studio Code 工具

这些应用程序和服务相结合，有助于显著加快数据科学项目的开发和部署进度。 

![Azure 机器学习的概念](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>开源兼容

Azure 机器学习完全支持开源技术。 可以使用几万个开源 Python 包，例如以下机器学习框架：

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

可以在 Docker 容器和 Spark 群集等托管环境中执行试验。 还可以使用高级硬件（例如 [Azure 中已启用 GPU 的虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu)）来加快执行速度。

Azure 机器学习构建在以下开源技术的基础之上：

- [Jupyter 笔记本](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

它还包含 Microsoft 自有的开源技术，例如[用于 Apache Spark 的 Microsoft 机器学习库](https://github.com/Azure/mmlspark)和认知工具包。

此外，我们还可以受益于 Microsoft 开发的、旨在解决大规模问题的一些先进且经过证实的机器学习技术。 这些技术在如下所述的许多 Microsoft 产品中经过实战性的测试：

- Windows
- 必应
- Xbox
- Office
- SQL Server

下面是 Azure 机器学习随附的一些 Microsoft 机器学习技术：

- [PROSE](https://microsoft.github.io/prose/)（使用示例的程序合成）
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench 是受 Windows 和 macOS 支持的桌面应用程序与命令行工具。 使用该软件可在整个数据科学生命周期内对机器学习解决方案进行管理：

- 数据引入和准备
- 模型开发和试验管理
- 不同目标环境中的模型部署

下面是 Azure Machine Learning Workbench 提供的核心功能：

- 可通过示例学习数据转换逻辑的数据准备工具。
- 可通过 UX 和 Python 代码访问的数据源抽象。
- 用于直观调用构造数据准备包的 Python SDK。
- 内置的 Jupyter Notebook 服务和客户端 UX。
- 通过运行历史记录视图进行试验监视和管理。
- 可通过 Azure Active Directory 实现共享和协作的基于角色的访问控制。
- 每个运行的自动项目快照，以及通过 Git 本机集成实现的显式版本控制。 
- 与流行的 Python IDE 集成。

有关详细信息，请参阅以下文章：
- [数据准备用户指南](data-prep-user-guide.md)
- [将 Git 与 Azure 机器学习配合使用](using-git-ml-project.md)
- [在 Azure 机器学习中使用 Jupyter Notebook](how-to-use-jupyter-notebooks.md)
- 漫游和共享
- [运行历史记录指南](how-to-use-run-history-model-metrics.md)
- [IDE 集成](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Azure 机器学习试验服务
试验服务处理机器学习试验的执行。 它还通过提供项目管理、Git 集成、访问控制、漫游和共享来支持 Workbench。 

通过简单的配置，可以跨各种计算环境选项执行试验：

- 本机环境
- 本地 Docker 容器
- 远程 VM 上的 Docker 容器
- 扩展 Azure 中的 Spark 群集

试验服务构造虚拟环境，确保脚本可在隔离环境中执行并生成可重现的结果。 该服务会记录运行历史记录信息，并直观显示历史记录。 可以从试验运行中轻松选择最佳模型。 

有关详细信息，请参阅[试验服务配置](experimentation-service-configuration.md)。

## <a name="azure-machine-learning-model-management-service"></a>Azure 机器学习模型管理服务

模型管理服务可让数据科学家和开发运营团队将预测模型部署到各种环境。 从训练运行到部署，模型版本和沿袭都可受到跟踪。 可在云中存储、注册和管理模型。 

使用简单的 CLI 命令，可在 Docker 映像中将模型、评分脚本和依赖项容器化。 这些映像会注册到托管在 Azure 上你自己的 Docker 注册表（Azure 容器注册表）中。 可将这些映像可靠部署到以下目标：

- 本地计算机
- 本地服务器
- 云
- IoT 边缘设备

Azure 容器服务 (ACS) 中运行的 Kubernetes 用于云中的扩展部署。 在 AppInsights 中捕获模型执行遥测数据，以进行可视分析。 

有关模型管理服务的详细信息，请参阅[模型管理概述](model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>用于 Apache Spark 的 Microsoft 机器学习库

[MMLSpark](https://github.com/Azure/mmlspark)（用于 Apache Spark 的 Microsoft 机器学习库）是针对 Apache Spark 提供深度学习和数据科学工具的开源 Spark 包。 它将 [Spark 机器学习管道](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)与 [Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)和 [OpenCV](http://opencv.org/) 库相集成。 使用它可为大型映像和文本数据集创建功能强大、高度可缩放的预测模型与分析模型。 部分亮点包括：

- 轻松将映像从 HDFS 引入 Spark 数据帧
- 使用 OpenCV 中的转换预处理映像数据
- 使用预先训练的深度神经网络和 Microsoft 认知工具包将映像特征化 
- 使用 Keras 的预先训练双向 LSTM 进行医疗实体提取
- 在 Azure 中的 N 系列 GPU VM 上训练基于 DNN 的映像分类模型
- 通过单个转换器，基于 SparkML 中的基元使用方便的 API 将自由格式文本数据特征化
- 通过数据的隐式特征化轻松训练分类和回归模型
- 计算丰富的一组评估指标，包括每个实例的指标

有关详细信息，请参阅[在 Azure 机器学习中使用 MMLSpark](how-to-use-mmlspark.md)。

## <a name="visual-studio-code-tools-for-ai"></a>用于 AI 的 Visual Studio Code 工具
用于 AI 的 Visual Studio Code 工具是 Visual Studio Code 中的一个扩展，用于生成、测试和部署深度学习与 AI 解决方案。 它提供 Azure 机器学习的多个集成点，包括：
- 运行历史记录视图，显示训练运行的性能和记录的指标。
- 库视图，可让用户使用 Microsoft 认知工具包、TensorFlow 及其他许多深度学习框架来浏览和启动新项目。 
- 资源管理器视图，用于选择脚本要执行的计算目标。
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Microsoft 提供哪些机器学习选项？
除了 Azure 机器学习以外，Azure 中还有多种选项可用于生成、部署和管理机器学习模型。 [在此处了解详细信息。](overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>后续步骤
* [安装和创建 Azure 机器学习](quickstart-installation.md)
