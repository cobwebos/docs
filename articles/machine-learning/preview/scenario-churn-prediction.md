---
title: "使用 Azure 机器学习进行客户流失预测 | Microsoft Docs"
description: "如何使用 Azure ML Workbench 执行客户流失分析。"
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 55298a7ff96bd9662310fa6b5d1764370f500be5
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="customer-churn-prediction-using-azure-machine-learning"></a>使用 Azure 机器学习进行客户流失预测

平均而言，留住现有客户的成本比开拓新客户的成本要低五倍。 因此，营销专员通常会尝试评估客户流失的可能性并确定可以尽量降低客户流失率的必要措施。

此解决方案的目标是展示如何使用 Azure Machine Learning Workbench 执行预测性客户流失分析。 此解决方案提供了一个易于使用的模板来为零售商开发客户流失预测数据管道。 此模板可以用于不同的数据集和不同的客户流失定义。 动手实验示例的目标是：

1. 了解如何使用 Azure Machine Learning Workbench 的数据准备工具来清理和引入用于客户流失分析的客户关系数据。

2. 执行功能转换来处理干扰性的异类数据。

3. 集成第三方库（例如 `scikit-learn` 和 `azureml`）来开发用于预测客户流失的基于 Bayesian 和基于树的分类器。

4. 部署。

## <a name="link-of-the-gallery-github-repository"></a>库 GitHub 存储库的链接
下面是存储了所有代码的公共 GitHub 存储库的链接：

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>用例概述
公司需要一个有效的策略来管理客户流失。 客户流失包括客户停止使用服务，改为使用竞争对手的服务，改为使用服务中的较低层体验，或者减少了对服务的参与。

在此用例中，我们将查看来自法国电信公司 Orange 的数据以查明近期可能会流失的客户以便改进服务并推出有助于留住客户的定制推广活动。

电信公司面临激烈的市场竞争。 许多运营商因为客户流失而失去了来自后付费客户的收入。 因此，能够准确查明客户流失可以成为一个巨大的竞争优势。

导致电信公司客户流失的一些因素包括：

* 感觉频繁的服务中断
* 在线/零售商店中糟糕的客户服务体验
* 其他竞争对手运营商推出的产品/服务（更好的家庭套餐、流量套餐，等等）。

在此解决方案中，我们将使用一个有关为电信公司构建预测性客户流失模型的具体示例。

## <a name="prerequisites"></a>先决条件

* [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）

* 按照[快速入门安装指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区

* 为实现操作化，最好是安装 Docker 引擎并在本地运行。 如果没有安装，可以使用群集选项，但请注意，运行 Azure 容器服务 (ACS) 可能比较昂贵。

* 该解决方案假设在本地安装有 Docker 引擎的 Windows 10 上运行 Azure Machine Learning Workbench。 如果使用的是 macOS，则操作说明大体上是相同的。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“客户流失预测”并选择模板
5.  单击“创建” 

## <a name="data-description"></a>数据说明

解决方案中使用的数据集来自 SIDKDD 2009 竞赛。 它名为 `CATelcoCustomerChurnTrainingSample.csv`，位于 [`Data`](https://github.com/mezmicrosoft/MachineLearningSamples-ChurnPrediction/tree/master/Data) 文件夹中。 该数据集包含来自法国电信公司 Orange 的干扰性异类数据（数字/分类变量）并且进行了匿名处理。

变量捕获客户人口统计信息、通话统计信息（例如，平均通话持续时间、呼叫失败率，等等）、合同信息、投诉统计信息。 客户流失变量是二进制的（0 - 未流失，1 - 已流失）。

## <a name="scenario-structure"></a>方案结构

文件夹结构的组织方式如下所述：

__Code__：包含与使用 Azure Machine Learning Workbench 进行客户流失预测相关的所有代码

__Data__：包含解决方案中使用的数据集  

__Labs__：包含所有动手实验

执行解决方案时动手实验的执行顺序如下所述：
1. 数据准备：code 文件夹中与数据准备相关的文件有 `CATelcoCustomerChurnTrainingSample.dprep`、`CATelcoCustomerChurnTrainingSample.dconn` 和 `CATelcoCustomerChurnTrainingSample.csv`
2. 建模和评估：code 文件夹中与建模和评估相关的主要文件为 `CATelcoCustomerChurnModeling.py`
3. Docker 中的建模和评估：code 文件夹中用于此任务的主要文件为 `CATelcoCustomerChurnModelingDocker.py`
4. 操作化：用于部署的主要文件为模型 (`model.pkl`) 和 `churn_schema_gen.py`

| 顺序| 文件名 | 相关的文件 |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/DataPreparation.md) | 'Data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/ModelingAndEvaluation.md) | 'Code/CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationDocker.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/ModelingAndEvaluationDocker.md) | 'Code/CATelcoCustomerChurnModelingDocker.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/Docs/Operationalization.md) | 'Code/model.pkl'<br>'Code/churn_schema_gen.py' |

按上文所述的顺序方式执行实验。

## <a name="conclusion"></a>结束语
此动手练习方案演示了如何使用 Azure Machine Learning Workbench 执行客户流失预测。 我们首先执行了数据清理来处理干扰性的异类数据，然后使用数据准备工具执行了特征工程。 我们使用开源机器学习工具创建了一个分类模型并对其进行了评估，然后使用本地 Docker 容器部署了该模型，使其可用于生产。

