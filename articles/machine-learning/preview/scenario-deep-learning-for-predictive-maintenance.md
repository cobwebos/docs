---
title: "深度学习预测性维护实际方案 - Azure | Microsoft Docs"
description: "了解如何重现有关使用 Azure Machine Learning Workbench 深度学习预防性维护的教程。"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 022e629469745fceb4fb9355cb6259a144dda222
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>深度学习预测性维护实际方案

深度学习是机器学习最流行的趋势之一，广泛运用于许多领域，包括：
- 无人驾驶汽车和机器人。
- 语音和图像识别。
- 金融预测。

这些方法也称为深度神经网络 (DNN)，受大脑中各种神经元（生物神经网络）的启发。

计划外设备停机带来的影响对任何企业而言都是极具破坏性的。 保持现场设备正常运行以最大限度提高利用率和性能，以及将开销和计划外停机时间降至最低至关重要。 极早发现问题所在有助于以经济高效的方式分配有限的维护资源，并改进质量和供应链流程。 

预测性维护 (PM) 策略使用机器学习方法来确定设备的状态，以便主动执行维护，避免对计算机造成不利的性能影响。 在 PM 中，在不同时间收集的数据用于监视计算机的状态，并通过分析找出用于预测故障的模式。 此设置中往往使用[长短期记忆 (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) 网络，因为它们能够从数据序列中学习。

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence 库 GitHub 存储库

PM 教程的 Cortana Intelligence 库是一个公共 GitHub 存储库（[https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)），你可以在其中报告问题并做出贡献。


## <a name="use-case-overview"></a>用例概述

本教程使用模拟飞机引擎运行故障事件示例来演示预测性维护的建模过程。 [预测性维护](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)中描述了此方案。

此设置的主要假设是引擎在其整个生存周期内会逐渐降级。 可以通过引擎传感器的度量值检测降级。 PM 会尝试对这些传感器值变化与历史故障之间的关系建模。 然后，该模型可以根据当前的传感器度量值状态，预测引擎将来何时可能发生故障。

此方案会创建 LSTM 网络，以使用历史传感器值预测飞机引擎的剩余工作寿命 (RUL)。 此方案使用包含 [Tensorflow](https://www.tensorflow.org/) 深度学习框架的 [Keras](https://keras.io/) 库作为计算引擎。 此方案使用一组引擎对 LSTM 进行训练，并基于一个从未用于训练的引擎集来测试网络。

## <a name="prerequisites"></a>先决条件
- [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。
- 创建有工作区的 Azure Machine Learning Workbench。
- 对于模型操作化：设置了本地部署环境的 Azure Machine Learning Operationalization，以及一个 [Azure 机器学习模型管理帐户](model-management-overview.md)。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：

1. 打开 Machine Learning Workbench。
2. 在“项目”页上选择 **+**，然后选择“新建项目”。
3. 在“新建项目”窗格中，输入新项目的信息。
4. 在“搜索项目模板”搜索框中，键入“预测性维护”并选择“适用于预测维护方案的深度学习”模板。
5. 选择“创建”。

## <a name="prepare-the-notebook-server-computation-target"></a>准备 Notebook 服务器计算目标

若要在本地计算机上运行，请从 Machine Learning Workbench 的“文件”菜单中选择“打开命令提示符”或“打开 PowerShell CLI”。 CLI 接口允许使用 `az` 命令访问 Azure 服务。 首先，使用以下命令登录到 Azure 帐户：

```
az login
``` 

此命令提供了要用于 https:\\aka.ms\devicelogin URL 的身份验证密钥。 CLI 将一直等待，直到设备登录操作返回并提供一些连接信息。 接下来，如果具有本地 [Docker](https://www.docker.com/get-docker) 安装，则使用以下命令准备本地计算环境：

```
az ml experiment prepare --target docker --run-configuration docker
```

考虑到内存和磁盘要求，最好在[用于 Linux (Ubuntu) 的数据科学虚拟机 (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) 上运行。 配置 DSVM 后，使用以下两个命令准备远程 Docker 环境：

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

连接到远程 Docker 容器后，使用以下命令准备 DSVM Docker 计算环境： 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

准备好 Docker 计算环境后，从 Machine Learning Workbench 的“Notebook”选项卡中打开 Jupyter Notebook 服务器，或者使用以下命令启动基于浏览器的服务器： 

```
az ml notebook start
```

示例 Notebook 存储在 Code 目录中。 各个 Notebook 设置为按顺序运行，从第一个 (Code\1_data_ingestion.ipynb) Notebook 开始。 打开每个 Notebook 时，系统会提示你选择计算内核。 选择 [Project_Name]_Template [Connection_Name] 内核以在之前配置的 DSVM 上执行。

## <a name="data-description"></a>数据说明

该模板使用 PM_train.txt、PM_test.txt 和 PM_truth.txt 文件中的三个数据集作为输入。 

- **训练数据**：飞机引擎运行故障数据。 训练数据 (PM_train.txt) 包含多个多元时序，以*周期*为时间单位。 它在每个周期中包含 21 个传感器读数。 

    - 将会基于相同类型的不同引擎生成每个时序。 每个引擎最初具有不同程度的初始磨损和某种独特的制造差异。 此信息对于用户是未知的。 

    - 在此模拟数据中，假定引擎在每个时序开始时正常运行。 在一系列运行周期中的某个时间点，它的性能开始下降。 性能不断下降，且下降速度呈数量级增长。 

    - 当达到预定义的阈值时，则认为该引擎不安全，无法再使用。 每个时序的最后一个周期是该引擎的故障点。

- **测试数据**：飞机引擎运行数据，不记录故障事件。 测试数据 (PM_test.txt) 的数据架构与训练数据相同。 唯一的区别在于该数据不指示故障发生时间（最后一个时间段*不*表示故障点）。 此引擎在出现故障前还可以持续多少个周期是未知的。

- **真实数据**：测试数据中每个引擎的真实剩余周期信息。 真实数据提供测试数据中引擎的剩余工作周期数。

## <a name="scenario-structure"></a>方案结构

方案工作流分为三个步骤，每个步骤在一个 Jupyter Notebook 中执行。 每个 Notebook 生成在本地保存的数据项目以用于 Notebook 中。

### <a name="task-1-data-ingestion-and-preparation"></a>任务 1：数据引入和准备

Code/1_data_ingestion_and_preparation.ipnyb 中的数据引入 Jupyter Notebook 将三个输入数据集加载成 Pandas 数据帧格式。 然后，该 Notebook 准备用于建模的数据，并进行一些初步数据可视化。 数据集存储在计算上下文的本地，以便在模型生成 Jupyter Notebook 中使用。

### <a name="task-2-model-building-and-evaluation"></a>任务 2：模型构建和评估

Code/2_model_building_and_evaluation.ipnyb 中的模型生成 Jupyter Notebook 从磁盘读取训练和测试数据集，并为训练数据集生成 LSTM 网络。 模型性能是根据测试数据集来衡量的。 生成的模型经过序列化，存储在本地计算上下文中，以用于操作化任务。

### <a name="task-3-operationalization"></a>任务 3：操作化

Code/3_operationalization.ipnyb 中的操作化 Jupyter Notebook 使用存储的模型来生成用于在 Azure 托管 Web 服务上调用模型的函数和架构。 该 Notebook 会测试这些函数，然后将资产压缩到 LSTM_o16n.zip 文件中。 此文件将加载到部署的 Azure 存储容器中。

LSTM_o16n.zip 部署文件包含以下项目：

- **webservices_conda.yaml**：定义在部署目标上运行 LSTM 模型所需的 Python 包。  
- **service_schema.json**：定义 LSTM 模型所需的数据架构。   
- **lstmscore.py**：定义部署目标运行的、用于给新数据评分的函数。    
- **modellstm.json**：定义 LSTM 体系结构。 lstmscore.py 函数读取体系结构和权重来初始化模型。
- **modellstm.h5**：定义模型权重。
- **test_service.py**：一个测试脚本，它调用包含测试数据记录的部署终结点。 
- **PM_test_files.pkl**：test_service.py 脚本从 PM_test_files.pkl 文件中读取历史引擎数据，并向 Web 服务发送 LSTM 的足够周期，以返回引擎故障的概率。

该 Notebook 在打包要部署的操作化资产之前，会使用模型定义测试函数。 Code/3_operationalization.ipnyb notebook 的末尾包含了有关设置和测试 Web 服务的说明。

## <a name="conclusion"></a>结束语

本教程提供了一个简单的方案，其中使用传感器值进行预测。 更高级的预防性维护方案，例如[预测性维护建模指导 R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1)，可以使用多个数据源，例如维护记录、错误日志和计算机特征。 其他数据源可能需要经过不同的处理才能在深度学习中使用。


## <a name="references"></a>参考

以下参考提供了适用于各种平台的其他预测性维护用例的示例：

* [预测性维护解决方案模板](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [预测性维护建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R Services 的预测性维护建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [预测性维护建模指南 Python 笔记本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的预测性维护](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [预测性维护实际方案](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>后续步骤

Machine Learning Workbench 中提供了其他示例方案，它们演示了产品的其他功能。 
