---
title: Microsoft 的其他机器学习产品 - Azure 机器学习 | Microsoft Docs
description: 除了 Azure 机器学习以外，Microsoft 还提供多种选项用于生成、部署和管理机器学习模型。
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 04/11/2018
ms.openlocfilehash: 3e36d9202c578294609b01eaf2731b1551ae67af
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282324"
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Microsoft 的其他机器学习产品和服务

除了 [Azure 机器学习](overview-what-is-azure-ml.md)以外，Microsoft 还提供多种选项用于生成、部署和管理机器学习模型。 
* SQL Server 机器学习服务
* Microsoft 机器学习服务器
* Azure 数据科学虚拟机
* HDInsight 中的 Spark MLLib
* Batch AI 训练服务
* Microsoft Cognitive Toolkit (CNTK)
* Azure 认知服务


## <a name="sql-server-machine-learning-services"></a>SQL Server 机器学习服务
借助 [SQL Server Microsoft 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)，可使用 R 或 Python 运行、训练和部署机器学习模型。 可以使用位于本地和 SQL Server 数据库中的数据。 

需要在本地或 Microsoft SQL Server 内部训练或部署模型时，可以使用 Microsoft 机器学习服务。 可以使用 Azure 机器学习模型管理来部署通过机器学习服务生成的模型。 

## <a name="microsoft-machine-learning-server"></a>Microsoft 机器学习服务器 
[Microsoft 机器学习服务器](https://docs.microsoft.com/en-us/machine-learning-server/what-is-machine-learning-server)是一个企业服务器，用于托管和管理 R 与 Python 进程的并行与分布式工作负荷。 Microsoft 机器学习服务器在 Linux、Windows、Hadoop 和 Apache Spark 上运行。 还可以在 [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/) 中使用它。 它针对使用 [Microsoft 机器学习包](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)生成的解决方案提供执行引擎，并通过对以下方案的支持扩展了开源 R 和 Python：

- 高性能分析
- 统计分析
- 机器学习
- 巨型数据集

通过可以连同服务器一起安装的专属包提供附加功能。 对于开发，可以使用[针对 Visual Studio 的 R 工具](https://www.visualstudio.com/vs/rtvs/)和[针对 Visual Studio 的 Python 工具](https://www.visualstudio.com/vs/python/)等 IDE。

有以下需求时，可以使用 Microsoft 机器学习服务器：

- 在服务器上生成和部署以 R 和 Python 编写的模型
- 在 Hadoop 或 Spark 群集上大规模分发 R 和 Python 训练

## <a name="azure-data-science-virtual-machine"></a>Azure 数据科学虚拟机
[数据科学虚拟机](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)是专为研究数据科学生成的 Microsoft Azure 云上的自定义虚拟机环境。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。 数据科学 VM 可用于 Windows Server 2016 和 2012 版，也可用于 Ubuntu 16.04 LTS 上以及基于 OpenLogic CentOS 7.4 的分发上的 Linux VM。 

需要在单个节点上运行或托管作业时，可以使用数据科学 VM。 或者，需要在单个计算机上远程提高处理能力时，也可以使用它。 支持将数据科学虚拟机用作 Azure 机器学习试验和 Azure 机器学习模型管理的目标。 

## <a name="spark-mllib-in-hdinsight"></a>HDInsight 中的 Spark MLLib
使用 [HDInsight 中的 Spark MLLib](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) 可以创建模型，作为针对大数据执行的 Spark 作业的一部分。 使用 Spark 可以轻松转换和准备数据，然后在单个作业中扩展模型创建。 可以使用 Azure 机器学习模型管理来部署、管理和监视通过 Spark MLLib 创建的模型。 可以使用 Azure 机器学习试验来调度和管理训练运行。 此外，还可以使用 Spark 来扩展在 Azure Machine Learning Workbench 中创建的数据准备作业。 

需要扩展数据处理并将模型创建为数据管道的一部分时，可以使用 Spark。 可以使用 Scala、Java、Python 或 R 创作 Spark 作业。 

## <a name="batch-ai-training"></a>Batch AI 训练 
借助 [Azure Batch AI 训练](https://aka.ms/batchaitraining)，可以使用任何框架在 AI 模型中进行并行试验，然后在群集化 GPU 中大规模训练这些模型。 你只需描述作业要求和要运行的配置，我们会处理剩余的工作。 

通过 Batch AI 训练，可以使用如下所述的框架在群集化 GPU 中扩展深度学习作业：

- 认知工具包
- Caffe
- Chainer
- TensorFlow

Azure 机器学习模型管理可用于从 Batch AI 训练中提取模型，以便对其进行部署、管理和监视。  Batch AI 训练将来会与 Azure 机器学习试验集成。 

## <a name="microsoft-cognitive-toolkit-cntk"></a>Microsoft Cognitive Toolkit (CNTK)
[Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)是统一的深度学习工具包，在定向图中以计算步骤的形式描述神经网络。 在此定向图中，叶节点表示输入值或网络参数，其他节点表示输入时的矩阵操作。 使用认知工具包可以轻松实现与合并常用的模型类型，例如前馈式 DNN、卷积神经网络 (CNN) 和循环神经网络 (RNN/LSTM)。 它能够在多个 GPU 和服务器上使用自动区分和并行化实现随机梯度下降（SGD，误差反向传播）式学习。

想要使用深度学习生成模型时，可以使用认知工具包。  可在上述任何服务中使用认知工具包。

## <a name="azure-cognitive-services"></a>Azure 认知服务
[Azure 认知服务](https://docs.microsoft.com/azure/#pivot=products&panel=ai)由大约 30 个 API 组成，可以生成使用自然通信方法的应用。 借助这些 API，只需编写几行代码，就能让应用看到、听到、讲出、理解和解释用户的需求。 将智能功能轻松添加到应用，例如： 

- 情感和观点检测
- 视觉和语音识别
- 语言理解 (LUIS)
- 知识和搜索

认知服务可用于开发跨设备和平台的应用。 API 不断改进，且易于设置。 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>后续步骤

阅读 [Azure 机器学习](overview-what-is-azure-ml.md)的概述。
