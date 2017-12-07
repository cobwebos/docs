---
title: "预测性维护实际方案 | Microsoft Docs"
description: "使用 PySpark 的预测性维护实际方案"
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
ms.openlocfilehash: 2687eb022bce0b71c217f0be611c8fabdfb66040
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>预测性维护实际方案

计划外设备停机带来的影响对任何企业而言都是极具破坏性的。 因此保持现场设备正常运行以最大限度提高利用率和性能，以及将开销和计划外停机时间降至最低至关重要。 极早发现问题所在有助于以经济高效的方式分配有限的维护资源，并改进质量和供应链流程。 

对于此方案，我们使用相对[规模较大的数据](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)来逐步指导用户完成从数据引入、到特征工程、再到模型构建最后到模型实施和部署等一系列过程的主要步骤。 整个过程的代码用 PySpark 编写，并使用 Jupyter 笔记本在 Azure ML Workbench 内实现。 最佳模型最终使用 Azure 机器学习模型管理实施，用于在生产环境中进行实时故障预测。   

## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接

以下是到公共 GitHub 存储库的链接：[https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>用例概述

重资产型行业企业面临的主要挑战是机械问题延误所导致的巨额成本。 大多数企业都有兴趣预测何时出现这些问题，以便在问题发生之前将其扼杀在摇篮里。 目标是减少停机时间，某些情况下还能提高安全性，从而降低成本。 有关常见用例和用于预测性维护的建模方法的详细说明，请参阅[预测性维护操作手册](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)。

此方案充分利用了操作手册的理念，目的是为了提供相关步骤以实现基于合成多个实际业务问题的方案的预测性模型。 此示例汇集了从许多预测性维护用例中观察到的常见数据元素。

这些模拟数据的业务问题是预测组件故障引发的问题。 因此，业务问题是“*由于组件故障而导致计算机故障的概率是多少*？” 将该问题格式化为多类分类问题（每个计算机多个组件），并使用机器学习算法来创建预测性模型。 基于从计算机中收集的历史数据定型模型。 在此方案中，用户将在 Azure Machine Learning Workbench 环境中完成实现此类模型的各种步骤。

## <a name="prerequisites"></a>先决条件

* [Azure 帐户](https://azure.microsoft.com/en-us/free/)（提供免费试用版）。
* 遵循[安装快速入门指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区。
* Azure 机器学习操作化需要一个本地部署环境和[模型管理帐户](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview)

本示例可在任何 AML Workbench 计算上下文中运行。 但是，建议在至少有 16 GB 内存的计算机上运行。 此方案已在运行远程 DS4_V2 标准大小的[适用于 Linux (Ubuntu) 的数据科学虚拟机](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)的 Windows 10 计算机上进行生成和测试。

模型操作化是使用 Azure 机器学习 CLI 0.1.0a22 版完成的。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“预测性维护”并选择模板
5.  单击“创建” 

## <a name="prepare-the-notebook-server-computation-target"></a>准备 Notebook 服务器计算目标

若要在本地计算机上运行，请在 AML Workbench `File` 菜单中选择 `Open Command Prompt` 或 `Open PowerShell CLI`。 在 CLI 窗口中执行以下命令：

`az ml experiment prepare --target docker --run-configuration docker`

建议在适用于 Linux (Ubuntu) 的数据科学虚拟机上运行。 配置 DSVM 后，运行以下两条命令：

`az ml computetarget attach remotedocker --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

准备 docker 映像后，在 AML Workbench 的 Notebook 选项卡中打开 Jupyter Notebook 服务器；若要启动基于浏览器的服务器，请运行：`az ml notebook start`。

Notebook 存储在 Jupyter 环境中的 `Code` 目录内。 我们按顺序运行 Notebook，从第一个 (`Code\1_data_ingestion.ipynb`) Notebook 开始。 打开每个 Notebook 时，系统会提示选择计算内核。 选择“[Project_Name]_Template [Desired_Connection_Name]”并单击“选择内核”。

## <a name="data-description"></a>数据说明

[模拟数据](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data)包含五个逗号分隔值 (.csv) 文件。 单击链接获取数据集的更详细说明。

* [计算机](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv)：区分每台计算机的特征。 例如，期限和模型。
* [错误](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv)：错误日志包含机器仍在运行时引起的不间断错误。 这些错误并不被视为故障，但它们可能是以后发生故障事件的前兆。 由于遥测数据以小时的速率收集，错误的日期时间将舍入到最接近的小时。
* [维护](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv)： 维护日志包含计划性维护和非计划性维护记录。 计划性维护对应于组件的定期检查，非计划性维护可能源于机械故障或其他性能降低。 由于遥测数据以小时的速率收集，维护的日期时间将舍入到最接近的小时。
* [遥测](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv)：遥测时序数据包含从每台机器实时收集的电压、旋转、压力和振动传感器测量结果。 数据是一小时内的平均数据并存储在遥测日志中
* [故障](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv)：故障对应于维护日志中的组件更换。 每个记录都包含机器 ID、组件类型以及更换日期和时间。 这些记录用于创建模型尝试预测的机器学习标签。

请参阅“代码”部分中的[数据引入](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Jupyter 笔记本方案，以便从 GitHub 存储库中下载原始数据集并为此分析创建 PySpark 数据集。

## <a name="scenario-structure"></a>方案结构
该方案的内容在 [GitHub 存储库](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)中提供。 

在存储库中，有一个[自述](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md)文件，其中概述了从准备数据到构建几个模型、然后最终实施最佳模型之一的过程。 存储库内的 [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) 文件夹中提供了四个 Jupyter 笔记本。   

接下来我们将介绍分步方案工作流。 端到端方案以 PySpark 编写，划分为四个 Notebook：

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb)：此 Notebook 下载五个 .csv 输入文件，执行一些初步的数据清理和可视化。 此 Notebook 将数据转换为 PySpark 格式，并将结果存储在 Azure blob 容器中，以便在特征工程任务中使用。

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb)：使用在上一步骤中清理的数据集，为遥测传感器创建延隔聚合特征，并将误差计数、组件更换和计算机信息联接到遥测数据。 故障相关的组件更换用于构造标签来描述哪个组件发生了故障。 带标签的特征数据保存在 Azure Blob 中，供建模任务使用。

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb)：建模 Notebook 使用带标签的特征数据集根据日期时间戳将数据拆分为训练和开发数据集。 使用 `pyspark.ml.classification` 模型为该 Notebook 设置集试验。 训练数据已向量化，用户可以使用 `DecisionTreeClassifier` 或 `RandomForestClassifier` 进行试验，并通过操作超参数来找到性能最佳的模型。 通过评估开发数据集中的度量统计信息来确定性能。 这些统计信息会记录回到 AML Workbench 运行时屏幕用于跟踪。 每次运行时，该 Notebook 会将生成的模型保存到运行 Jupyter Notebook 内核的本地磁盘。 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb)：此 Notebook 使用已保存到本地（Jupyter Notebook 内核）磁盘的最后一个模型生成组件，用于在 Azure Web 服务中将模型操作化。 整个操作资产会压缩到在另一个 Azure Blob 容器中存储的 `o16n.zip` 文件。 该 zip 文件包含：

* `service_schema.json`：部署的架构定义文件。 
* `pdmscore.py`：Azure Web 服务所需的 init() 和 run() 函数
* `pdmrfull.model`：模型定义目录。
    
 Notebook 在打包要部署的操作化资产之前，会使用模型定义测试函数。 Notebook 的末尾包含部署说明。

## <a name="conclusion"></a>结束语

此方案为读者简要介绍了如何在 Azure ML Workbench 的 Jupyter 笔记本环境中使用 PySpark 构建端到端预测性维护解决方案。 此方案还指导读者了解如何使用 Azure 机器学习模型管理环境轻松实施和部署最佳模型，以便在生产环境中进行实时故障预测时使用。 然后，读者可以编辑方案的相关部分以根据业务需求逐渐修改。  

## <a name="references"></a>参考

本用例事先已在多个平台上开发：

* [预测性维护解决方案模板](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [预测性维护建模指南](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [使用 SQL R Services 的预测性维护建模指南](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [预测性维护建模指南 Python 笔记本](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [使用 PySpark 的预测性维护](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>后续步骤

Azure Machine Learning Workbench 中提供了其他许多示例方案来演示产品的其他功能。 