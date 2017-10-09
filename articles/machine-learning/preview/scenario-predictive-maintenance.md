--- 
title: "预测性维护实际方案 | Microsoft Docs"
description: "使用 PySpark 的预测性维护实际方案"
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>预测性维护实际方案


计划外设备停机带来的影响对任何企业而言都是极具破坏性的。 因此保持现场设备正常运行以最大限度提高利用率和性能，以及将开销和计划外停机时间降至最低至关重要。 极早发现问题所在有助于以经济高效的方式分配有限的维护资源，并改进质量和供应链流程。 

对于此方案，我们使用相对[规模较大的数据](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)来逐步指导用户完成从数据引入、到特征工程、再到模型构建最后到模型实施和部署等一系列过程的主要步骤。 整个过程的代码用 PySpark 编写，并使用 Jupyter 笔记本在 Azure ML Workbench 内实现。 最佳模型最终使用 Azure 机器学习模型管理实施，用于在生产环境中进行实时故障预测。   

## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接

以下是到公共 GitHub 存储库的链接：[https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>用例概述

重资产型行业企业面临的主要挑战是机械问题延误所导致的巨额成本。 大多数企业都有兴趣预测何时出现这些问题，以便在问题发生之前将其扼杀在摇篮里。 这可以减少停机时间，某些情况下还能提高安全性，从而降低成本。 有关常见用例和预测性维护建模方法的详细说明，请参阅[预测性维护操作手册](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)。

此方案充分利用了操作手册的理念，目的是为了提供相关步骤以实现基于合成多个实际业务问题的方案的预测性模型。 此示例汇集了从许多预测性维护用例中观察到的常见数据元素。

这些模拟数据的业务问题是预测组件故障引发的问题。 因此，业务问题是“*由于组件故障而导致计算机故障的概率是多少*？” 将该问题格式化为多类分类问题（每个计算机多个组件），并使用机器学习算法来创建预测性模型。 基于从计算机中收集的历史数据定型模型。 在此方案中，用户将在 Azure Machine Learning Workbench 环境中完成实现此类模型的各种步骤。

## <a name="prerequisites"></a>先决条件

* [Azure 帐户](https://azure.microsoft.com/en-us/free/)（提供免费试用版）。
* 按照[快速入门安装指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区。
* 在此方案中，跨 Jupyter 笔记本使用的中间结果存储在 Azure Blob 存储容器中。 此[链接](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage)提供了有关设置 Azure 存储帐户的说明。 
* 为[实施](https://github.com/Azure/Machine-Learning-Operationalization)模型，用户最好是安装了 [Docker 引擎](https://www.docker.com/)并在本地运行。 如果没有安装，可以使用群集选项，但请注意，运行 [Azure 容器服务 (ACS)](https://azure.microsoft.com/en-us/services/container-service/) 可能通常成本高昂。
* 此方案假设用户在本地装有 Docker 引擎的 Windows 10 计算机上运行 Azure ML Workbench。 
* 该方案是在具有以下规格的 Windows 10 计算机上构建和测试的：Intel Core i7-4600U CPU @ 2.10 GHz，8-GB RAM，64 位操作系统，基于 x64 的 Docker 版本 17.06.0-ce-win19 处理器（12801）。 
* 使用此版本的 Azure ML CLI 完成模型实施：azure-cli-ml==0.1.0a22

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

将本示例用作模板，创建新的项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“预测性维护”并选择模板
5.  单击“创建” 

## <a name="data-description"></a>数据说明

[模拟数据](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五个逗号分隔值 (.csv) 文件。 

* [计算机](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：区分每台计算机的特征。 例如，期限和模型。
* [错误](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：错误日志包含机器仍在运行时引起的不间断错误。 这些错误并不被视为故障，但它们可能是以后发生故障事件的前兆。 由于遥测数据以小时的速率收集，错误的日期时间将舍入到最接近的小时。
* [维护](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)： 维护日志包含计划性维护和非计划性维护记录。 计划性维护对应于组件的定期检查，非计划性维护可能源于机械故障或其他性能降低。 由于遥测数据以小时的速率收集，维护的日期时间将舍入到最接近的小时。
* [遥测](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)：遥测时序数据包含从每台机器实时收集的电压、旋转、压力和振动传感器测量结果。 数据是一小时内的平均数据并存储在遥测日志中
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障对应于维护日志中的组件更换。 每个记录都包含机器 ID、组件类型以及更换日期和时间。 这些记录用于创建模型尝试预测的机器学习标签。

请参阅“代码”部分中的[数据引入](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 笔记本方案，以便从 GitHub 存储库中下载原始数据集并为此分析创建 PySpark 数据集。

## <a name="scenario-structure"></a>方案结构
该方案的内容在 [GitHub 存储库](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)中提供。 

在存储库中，有一个[自述](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md)文件，其中概述了从准备数据到构建几个模型、然后最终实施最佳模型之一的过程。 存储库内的 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 文件夹中提供了四个 Jupyter 笔记本。   

接下来我们将介绍分步方案工作流。 端到端方案在 PySpark 中编写，分为四个笔记本，如下所述：

* [数据引入](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此笔记本处理五个输入 .csv 文件的数据引入，执行一些初步清理，创建一些汇总图形来验证数据下载，最后将生成的数据集存储在 Azure blob 容器中供下一个笔记本使用。

* [特征工程](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)：使用上一步清理的数据集，为遥测传感器创建滞后特征，连同其他特征工程创建变量（例如自上次更换以来的天数）。 最后，将故障标记为相关记录，以创建最终数据集并保存在 Azure blob 中供下一步使用。 

* [模型构建](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)：然后，将最终的特征工程数据集拆分为两个，即基于日期时间戳的定型和测试数据集。 然后，在定型数据集上构建两个模型，即随机森林分类器和决策树分类器，然后对测试数据集进行评分。 

* [模型实施和部署](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)：然后，将上一步中构建的最佳模型保存为 .model 文件，连同相关的 .json 方案文件用于部署。 init() 和 run() 函数首先在本地进行测试，然后使用 Azure 机器学习模型管理环境实施模型，以便在生产环境中进行实时故障预测使用。  

## <a name="conclusion"></a>结束语

此方案为读者简要介绍了如何在 Azure ML Workbench 的 Jupyter 笔记本环境中使用 PySpark 构建端到端预测性维护解决方案。 此方案还指导读者了解如何使用 Azure 机器学习模型管理环境轻松实施和部署最佳模型，以便在生产环境中进行实时故障预测时使用。 然后，读者可以编辑方案的相关部分以根据业务需求逐渐修改。  

## <a name="references"></a>参考

此用例以前在多个平台上开发，如下所示：

* [预测性维护解决方案模板](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [预测性维护建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R Services 的预测性维护建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [预测性维护建模指南 Python 笔记本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的预测性维护](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



