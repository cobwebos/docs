---
title: "深度学习预测性维护实际方案 - Azure | Microsoft Docs"
description: "了解如何重现有关使用 Azure Machine Learning Workbench 深度学习预防性维护的教程。"
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>深度学习预测性维护实际方案

深度学习是机器学习最流行的趋势之一。 深度学习广泛运用于许多领域和应用程序，其中包括无人驾驶车、语音和图像识别、机器人学和金融。 深度学习是受大脑形状（生物神经网络）和机器学习启发的一组算法。 认知科学通常将深度学习称为人工神经网络 (ANN)。

预测性维护也很受欢迎。 在预测性维护中，可通过多种不同的方法帮助确定设备状况，并预测何时应执行维护。 预测性维护的一些常见用途包括故障预测、故障诊断（根本原因分析）、故障检测、故障类型分类和发生故障后的缓解措施或维护操作建议。

在预测性维护方案中，将收集一段时间内的数据来监视设备状态。 目标是找到可帮助预测和最终防止出现故障的模式。 使用[长短期记忆 (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) 网络是一种在预测性维护中尤其受欢迎的深度学习方法。 LSTM 网络擅长学习序列。 时序数据可用于回顾较长一段时间来检测故障模式。

在本教程中，我们将为[预测性维护](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)中所述的数据集和方案构建一个 LSTM 网络。 我们使用该网络来预测飞机引擎的剩余使用寿命。 该模板使用模拟飞机传感器值来预测飞机引擎将在未来哪个时间点出现故障。 利用此预测，可以事先计划维护，以防出现故障。

本教程使用 [Keras](https://keras.io/) 深度学习库，并使用 Microsoft 认知工具包 [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) 作为后端。

包含本教程相关示例的公共 GitHub 存储库位于 [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 上。

## <a name="use-case-overview"></a>用例概述

本教程使用模拟飞机引擎运行故障事件示例来演示预测性维护的建模过程。 

此处所述的建模数据隐式假设是指资产采用累进降级模式。 该模式反映在资产的传感器度量中。 通过检查资产一段时间内的传感器值，机器学习算法可以学习传感器值、传感器值变化与历史故障之间的关系。 此关系用于预测未来的故障。 

在将示例数据替换成你自己的日期之前，建议先检查数据格式，并完成模板的所有三个步骤。

## <a name="prerequisites"></a>系统必备

- [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。
- 创建有工作区的 Azure Machine Learning Workbench。
- 对于模型操作化：设置了本地部署环境的 Azure Machine Learning Operationalization，以及一个 [Azure 机器学习模型管理帐户](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：

1. 打开 Machine Learning Workbench。
2. 在“项目”页上选择 **+**，然后选择“新建项目”。
3. 在“新建项目”窗格中，输入新项目的信息。
4. 在“搜索项目模板”搜索框中，输入“预测性维护”，然后选择模板。
5. 选择“创建”。

## <a name="prepare-the-notebook-server-computation-target"></a>准备 Notebook 服务器计算目标

在本地计算机上，从 Machine Learning Workbench 的“文件”菜单中选择“打开命令提示符”或“打开 PowerShell”。 在所选选项的命令提示符窗口中，执行以下命令：

`az ml experiment prepare --target docker --run-configuration docker`

建议在 DS4_V2 标准 [Data Science Virtual Machine (DSVM) for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) 上运行 Notebook 服务器。 配置完 DSVM 之后，运行以下命令来准备 Docker 映像：

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

准备好 Docker 映像后，打开 Jupyter Notebook 服务器。 若要打开 Jupyter Notebook 服务器，请转到 Machine Learning Workbench 的“Notebook”选项卡，或启动基于浏览器的服务器：`az ml notebook start`

Notebook 存储在 Jupyter 环境中的 Code 目录内。 按编号顺序运行这些 Notebook，先从 Code\1_data_ingestion_and_preparation.ipynb 开始。

选择与 [project_name]_Template [connection_name] 匹配的内核，然后选择“设置内核”。

## <a name="data-description"></a>数据说明

该模板使用 PM_train.txt、PM_test.txt 和 PM_truth.txt 文件中的三个数据集作为输入。

-  **训练数据**：飞机引擎运行故障数据。 训练数据 (PM_train.txt) 包含多个多元时序，以*周期*为时间单位。 它在每个周期中包含 21 个传感器读数。 

    可以假定每个时序生成自同一类型的不同引擎。 假定每个引擎一开始都具有不同程度的初始磨损和制造变化。 此信息对于用户是未知的。 

    在此模拟数据中，假定引擎在每个时序开始时正常运行。 在一系列运行周期中的某个时间点，它的性能开始下降。 性能不断下降，且下降速度呈数量级增长。 

    当达到预定义的阈值时，则认为该引擎不安全，无法再使用。 每个时间序列中的最后一个周期都可被视为相应引擎的故障点。

-   **测试数据**：飞机引擎运行数据，不记录故障事件。 测试数据 (PM_test.txt) 的数据架构与训练数据相同。 唯一的区别在于该数据不指示故障发生时间（最后一个时间段*不*表示故障点）。 此引擎在出现故障前还可以持续多少个周期是未知的。

- **真实数据**：测试数据中每个引擎的真实剩余周期信息。 真实数据提供测试数据中引擎的剩余工作周期数。

## <a name="scenario-structure"></a>方案结构

[GitHub 存储库] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)提供了该方案的相关内容。 

在存储库中，[自述文件] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md)概述了从准备数据到构建和实现模型的一系列过程。 存储库中的 [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) 文件夹包含了这三个 Jupyter Notebook。 

接下来我们将介绍分步方案工作流。

### <a name="task-1-data-ingestion-and-preparation"></a>任务 1：数据引入和准备

Code/1_data_ingestion_and_preparation.ipnyb 中的数据引入 Jupyter Notebook 将三个输入数据集加载成 Pandas 数据帧格式。 接着，它准备用于建模的数据，并进行一些初步数据可视化。 之后，数据转换成 PySpark 格式，并存储在 Azure 订阅的 Azure Blob 存储容器中，用于下一个建模任务。

### <a name="task-2-model-building-and-evaluation"></a>任务 2：模型构建和评估

Code/2_model_building_and_evaluation.ipnyb 中的模型构建 Jupyter Notebook 从 Blob 存储中读取 PySpark 训练和测试数据集。 然后，使用训练数据集构建 LSTM 网络。 模型性能根据测试集来衡量。 生成的模型经过序列化，存储在本地计算上下文中，以用于操作化任务。

### <a name="task-3-operationalization"></a>任务 3：操作化

Code/3_operationalization.ipnyb 中的操作化 Jupyter Notebook 使用存储的模型来生成在 Azure 托管 Web 服务上调用模型所需的函数和架构。 该 Notebook 会测试这些函数，然后将操作化资产压缩成 .zip 文件。

压缩后的文件包含以下文件：

- **modellstm.json**：用于部署的架构定义文件。 
- **lstmscore.py**：Azure Web 服务所需的 **init()** 和 **run()** 函数。
- **lstm.model**：模型定义目录。

该 Notebook 在打包要部署的操作化资产之前，会使用模型定义测试函数。 Notebook 的末尾包含部署说明。


## <a name="conclusion"></a>结束语

本教程使用的方案较为简单，该方案仅使用一个数据源（传感器值）进行预测。 在更高级的预测性维护方案中，比如[预测性维护建模指南 R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)，可能会使用多个数据源。 其他数据源可能包括历史维护记录、错误日志以及机器和操作员特征。 其他数据源可能需要在深度学习网络中进行不同类型的处理。 此外，优化模型以使用正确的参数（例如窗口大小）也很重要。 

可以编辑此方案的相关部分，并尝试其他难处理的方案，比如[预测性维护建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)（涉及其他多个数据源）中所述的方案。


## <a name="references"></a>参考

- [预测性维护解决方案模板](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [预测性维护建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [预测性维护建模指南 Python 笔记本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [使用 PySpark 的预测性维护](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

