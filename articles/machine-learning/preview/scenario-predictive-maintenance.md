---
title: "实际方案的预测性维护 | Microsoft Docs"
description: "使用 PySpark 进行实际方案的预测性维护"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 81e227194ff64d7b7af842a208349ccc63528ab8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>实际方案的预测性维护

计划外设备停机带来的影响对任何企业而言都是极具破坏性的。 保持现场设备正常运行以最大限度提高利用率和性能，以及将开销和计划外停机时间降至最低至关重要。 极早发现问题所在有助于以经济高效的方式分配有限的维护资源，并改进质量和供应链流程。 

此方案利用一个相当[大规模的模拟数据集](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)来指导用户逐步完成一个预测性维护数据科学项目，包括从数据引入、特征工程、模型构建直到模型实施和部署的一系列步骤。 整个过程的代码都是在 Azure Machine Learning Workbench 内在 Jupyter Notebook 中使用 PySpark 编写的。 最终模型是使用“Azure 机器学习模型管理”部署的，以实现实时的设备故障预测。   

### <a name="cortana-intelligence-gallery-github-repository"></a>Cortana Intelligence 库 GitHub 存储库

PM 教程的 Cortana Intelligence 库是一个公共 GitHub 存储库（[https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)），可在其中报告问题并做出贡献。


## <a name="use-case-overview"></a>用例概述

重资产型行业企业面临的主要挑战是机械问题延误所导致的巨额成本。 大多数企业都有兴趣预测何时出现这些问题，以便在问题发生之前将其扼杀在摇篮里。 目标是减少停机时间，某些情况下还能提高安全性，从而降低成本。 

此方案根据[预测性维护战术手册](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)中的理念来演示如何为模拟的数据集创建预测性模型。 示例数据来自许多预测性维护用例中的常用元素。

这些模拟数据的业务问题是预测组件故障引发的问题。 业务问题是“由于组件故障而导致计算机故障的概率是多少？” 将该问题格式化为多类分类问题（每个计算机多个组件）。 使用机器学习算法来创建预测模型。 基于从计算机中收集的历史数据定型模型。 在此方案中，用户将在 Machine Learning Workbench 环境中完成实现此类模型的各种步骤。

## <a name="prerequisites"></a>先决条件

* [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。
* [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的已安装副本。 若要安装此程序并创建工作区，请参阅[快速入门安装指南](./quickstart-installation.md)。
* Azure 机器学习操作化需要一个本地部署环境和 [Azure 机器学习模型管理帐户](model-management-overview.md)。

本示例可在任何 Machine Learning Workbench 计算上下文中运行。 但是，建议在至少有 16 GB 内存的计算机上运行。 此方案已在运行远程 DS4_V2 标准大小的[适用于 Linux (Ubuntu) 的数据科学虚拟机 (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) 的 Windows 10 计算机上进行生成和测试。

模型操作化是使用 Azure 机器学习 CLI 0.1.0a22 版完成的。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Machine Learning Workbench。
2.  在“项目”页上选择 **+**，然后选择“新建项目”。
3.  在“新建项目”窗格中，填写新项目的信息。
4.  在“搜索项目模板”搜索框中，键入“预测性维护”并选择“预测性维护”模板。
5.  选择“创建”。

## <a name="prepare-the-notebook-server-computation-target"></a>准备 Notebook 服务器计算目标

若要在本地计算机上运行，请从 Machine Learning Workbench 的“文件”菜单中选择“打开命令提示符”或“打开 PowerShell CLI”。 CLI 接口允许使用 `az` 命令访问 Azure 服务。 首先，使用以下命令登录到 Azure 帐户：

```
az login
``` 

此命令提供了要用于 https:\\aka.ms\devicelogin URL 的身份验证密钥。 CLI 将一直等待，直到设备登录操作返回并提供一些连接信息。 接下来，如果具有本地 [Docker](https://www.docker.com/get-docker) 安装，则使用以下命令准备本地计算环境：

```
az ml experiment prepare --target docker --run-configuration docker
```

考虑到内存和磁盘要求，最好在[用于 Linux (Ubuntu) 的 DSVM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) 上运行。 配置 DSVM 后，使用以下两个命令准备远程 Docker 环境：

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

连接到远程 Docker 容器后，使用以下命令准备 DSVM Docker 计算环境： 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

准备好 Docker 计算环境后，从 Azure Machine Learning Workbench 的“Notebook”选项卡中打开 Jupyter Notebook 服务器，或者使用以下命令启动基于浏览器的服务器： 

```
az ml notebook start
```

示例 Notebook 存储在 Code 目录中。 各个 Notebook 设置为按顺序运行，从第一个 (Code\1_data_ingestion.ipynb) Notebook 开始。 打开每个 Notebook 时，系统会提示你选择计算内核。 选择 [Project_Name]_Template [Connection_Name] 内核以在之前配置的 DSVM 上执行。

## <a name="data-description"></a>数据说明

[模拟数据](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五个逗号分隔值 (.csv) 文件。 使用以下链接获取有关数据集的更详细说明。

* [计算机](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：区分每台计算机的特征，例如，期限和模型。
* [错误](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：错误日志包含机器仍在运行时引起的不间断错误。 这些错误并不被视为故障，但它们可能是以后发生故障事件的前兆。 由于遥测数据以小时的速率收集，错误的日期时间值将舍入到最接近的小时。
* [维护](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)： 维护日志包含计划性维护和非计划性维护记录。 计划性维护对应于组件的定期检查。 非计划性维护可能源于机械故障或其他性能降低。 由于遥测数据以小时的速率收集，维护的日期时间值将舍入到最接近的小时。
* [遥测数据](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)：遥测数据包含来自每台计算机中的多个传感器的时序度量值。 所记录的数据是传感器值在每一小时间隔内的平均值。
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障对应于维护日志中的组件更换。 每个记录都包含机器 ID、组件类型以及更换日期和时间。 这些记录用于创建模型尝试预测的机器学习标签。

若要从 GitHub 存储库中下载原始数据集并为此分析创建 PySpark 数据集，请参阅 Code 文件夹中的[数据引入](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter Notebook 方案。

## <a name="scenario-structure"></a>方案结构
该方案的内容在 [GitHub 存储库](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)中提供。 

[Readme](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) 文件概述了从准备数据、构建模型直到部署用于生产的解决方案的整个工作流。 工作流的每个步骤都封装在存储库内 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 文件夹中的一个 Jupyter Notebook 中。   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此 Notebook 下载五个 .csv 输入文件，执行一些初步的数据清理和可视化。 此 Notebook 将每个数据集转换为 PySpark 格式，并将结果存储在 Azure Blob 容器中，以便在特征工程 Notebook 中使用。

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)：模型特征是使用用于遥测数据、错误和维护数据的标准时序方法基于 Azure Blob 存储中的原始数据集构造的。 故障相关的组件更换用于构造模型标签来描述哪个组件发生了故障。 带标签的特征数据保存在 Azure Blob 中，供建模 Notebook 使用。

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)：建模 Notebook 使用带标签的特征数据集根据日期时间戳将数据拆分为训练和开发数据集。 使用 pyspark.ml.classification 模型为该 Notebook 设置试验。 训练数据已矢量化。 用户可以使用 **DecisionTreeClassifier** 或 **RandomForestClassifier** 进行试验，并通过操作超参数来找到性能最佳的模型。 通过评估开发数据集中的度量统计信息来确定性能。 这些统计信息会记录回到 Machine Learning Workbench 运行时屏幕用于跟踪。 每次运行时，该 Notebook 会将生成的模型保存到运行 Jupyter Notebook 内核的本地磁盘。 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)：此 Notebook 使用已保存到本地（Jupyter Notebook 内核）磁盘的最后一个模型来构建组件，用于将模型部署到 Azure Web 服务。 整个操作资产会压缩到在另一个 Azure Blob 容器中存储的 o16n.zip 文件。 该 zip 文件包含：

* **service_schema.json**：用于部署的架构定义文件。 
* **pdmscore.py**：Azure Web 服务所需的 **init()** 和 **run()** 函数。
* **pdmrfull.model**：模型定义目录。
    
Notebook 在打包要部署的操作化资产之前，会使用模型定义测试函数。 Notebook 的末尾包含部署说明。

## <a name="conclusion"></a>结束语

此方案概述了如何在 Machine Learning Workbench 的 Jupyter Notebook 环境中使用 PySpark 构建端到端预测性维护解决方案。 此示例方案还详细介绍了通过“机器学习模型管理”环境进行实时设备故障预测的模型部署。

## <a name="references"></a>参考

以下参考提供了适用于各种平台的其他预测性维护用例的示例：

* [预测性维护解决方案模板](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [预测性维护建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R Services 的预测性维护建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [预测性维护建模指南 Python 笔记本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的预测性维护](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [深度学习预测性维护](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>后续步骤

Machine Learning Workbench 中提供了其他示例方案，它们演示了产品的其他功能。 
