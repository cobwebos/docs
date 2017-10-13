---
title: "什么是 Azure 机器学习？ | Microsoft Docs"
description: "概述 Azure 机器学习试验和模型管理 - 这是一个集成式的端到端数据科学解决方案，可让专业数据科学家以云的规模开发、试验和部署高级分析应用程序。"
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 3bf9227a7ee432f036c57dd9d1c3807c7a867f3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
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

有关详细信息，请参阅[试验执行配置](experiment-execution-configuration.md)。

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
除了 Azure 机器学习以外，Azure 中还有多种选项可用于生成、部署和管理机器学习模型。 
* SQL Server 中的 Microsoft 机器学习服务
* Microsoft 机器学习服务器
* 数据科学虚拟机
* HDInsight 中的 Spark MLLib
* Batch AI 训练服务
* Microsoft 认知工具包
* Microsoft 认知服务


### <a name="microsoft-machine-learning-services-in-sql-server"></a>SQL Server 中的 Microsoft 机器学习服务
借助 [Microsoft 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)，可以使用 R 或 Python 运行、训练和部署机器学习模型。 可以使用位于本地和 SQL Server 数据库中的数据。 

需要在本地或 Microsoft SQL Server 内部训练或部署模型时，可以使用 Microsoft 机器学习服务。 可以使用 Azure 机器学习模型管理来部署通过机器学习服务生成的模型。 

### <a name="microsoft-machine-learning-server"></a>Microsoft 机器学习服务器 
[Microsoft 机器学习服务器](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)是一个企业服务器，用于托管和管理 R 与 Python 进程的并行与分布式工作负荷。 Microsoft 机器学习服务器在 Linux、Windows、Hadoop 和 Apache Spark 上运行。 还可以在 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) 中使用它。 它针对使用 [Microsoft 机器学习包](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)生成的解决方案提供执行引擎，并通过对以下方案的支持扩展了开源 R 和 Python：

- 高性能分析
- 统计分析
- 机器学习
- 巨型数据集

通过可以连同服务器一起安装的专属包提供增值功能。 对于开发，可以使用[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/vs/rtvs/)和[针对 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)等 IDE。

有以下需求时，可以使用 Microsoft 机器学习服务器：

- 在服务器上生成和部署以 R 和 Python 编写的模型
- 在 Hadoop 或 Spark 群集上大规模分发 R 和 Python 训练

### <a name="data-science-virtual-machine"></a>数据科学虚拟机
[数据科学虚拟机 (DSVM)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) 是专为研究数据科学生成的 Microsoft Azure 云上的自定义 VM 映像。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。 它在 Windows Server 和 Linux 上可用。 我们在 Server 2016 和 Server 2012 上提供了 Windows 版本的 DSVM。 我们在 Ubuntu 16.04 LTS 中和基于 OpenLogic 7.2 CentOS 的 Linux 发行版上提供了 Linux 版本的 DSVM。 

需要在单个节点上运行或托管作业时，可以使用数据科学虚拟机。 或者，需要在单个计算机上远程扩展处理能力时，也可以使用它。 支持将数据科学虚拟机用作 Azure 机器学习试验和 Azure 机器学习模型管理的目标。 

### <a name="spark-mllib-in-hdinsight"></a>HDInsight 中的 Spark MLLib
使用 [HDInsight 中的 Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) 可以创建模型，作为针对大数据执行的 Spark 作业的一部分。 使用 Spark 可以轻松转换和准备数据，然后在单个作业中扩展模型创建。 可以使用 Azure 机器学习模型管理来部署、管理和监视通过 Spark MLLib 创建的模型。 可以使用 Azure 机器学习试验来调度和管理训练运行。 此外，还可以使用 Spark 来扩展在 Azure Machine Learning Workbench 中创建的数据准备作业。 

需要扩展数据处理并将模型创建为数据管道的一部分时，可以使用 Spark。 可以使用 Scala、Java、Python 或 R 创作 Spark 作业。 

### <a name="batch-ai-training"></a>Batch AI 训练 
借助 [Azure Batch AI 训练](https://aka.ms/batchaitraining)，可以使用任何框架在 AI 模型中进行并行试验，然后在群集化 GPU 中大规模训练这些模型。 你只需描述作业要求和要运行的配置，我们会处理剩余的工作。 

通过 Batch AI 训练，可以使用如下所述的框架在群集化 GPU 中扩展深度学习作业：

- 认知工具包
- Caffe
- Chainer
- TensorFlow

Azure 机器学习模型管理可用于从 Batch AI 训练中提取模型，以便对其进行部署、管理和监视。  Batch AI 训练将来会与 Azure 机器学习试验集成。 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft 认知工具包
[Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)是统一的深度学习工具包，在定向图中以计算步骤的形式描述神经网络。 在此定向图中，叶节点表示输入值或网络参数，其他节点表示输入时的矩阵操作。 使用认知工具包可以轻松实现与合并常用的模型类型，例如前馈式 DNN、卷积神经网络 (CNN) 和循环神经网络 (RNN/LSTM)。 它能够在多个 GPU 和服务器上使用自动区分和并行化实现随机梯度下降（SGD，误差反向传播）式学习。

想要使用深度学习生成模型时，可以使用认知工具包。  可在上述任何服务中使用认知工具包。

### <a name="microsoft-cognitive-services"></a>Microsoft 认知服务
Microsoft 认知服务由 30 个 API 组成，可以生成使用自然通信方法的应用。 借助这些 API，只需编写几行代码，就能让应用看到、听到、讲出、理解和解释我们的需求。 将智能功能轻松添加到应用，例如： 

- 情感和观点检测
- 视觉和语音识别
- 语言理解
- 知识和搜索

Microsoft 认知服务可用于开发跨设备和平台的应用。 API 不断改进，且易于设置。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>后续步骤
* [安装和创建 Azure 机器学习](quickstart-installation.md)
