---
title: 什么是工作区
titleSuffix: Azure Machine Learning
description: 工作区是 Azure 机器学习的顶级资源。 它保留所有定型运行的历史记录，包括日志、指标、输出和脚本的快照。 您可以使用此信息来确定哪个训练运行生成最佳模型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 4891ec0ae87f0d7b0cd7e5d5d550f955a8fde1db
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312373"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>什么是 Azure 机器学习工作区？

工作区是 Azure 机器学习的顶级资源，提供了一个集中的位置来处理使用 Azure 机器学习时创建的所有项目。  工作区保存所有定型运行的历史记录，包括日志、指标、输出和脚本的快照。 使用此信息可以确定哪个训练运行产生最佳模型。  

使用所需的模型后，可将其注册到工作区。 然后，使用已注册的模型和评分脚本，将其部署到 Azure 容器实例、Azure Kubernetes 服务或基于 REST 的 HTTP 终结点作为现场可编程的入口数组（FPGA）。 你还可以将模型部署到 Azure IoT Edge 设备作为模块。

可用的定价和功能取决于是否为工作区选择了 "[基本" 或 "企业版](overview-what-is-azure-ml.md#sku)"。 您在[创建工作区](#create-workspace)时选择版本。  你还可以从 "基本"[升级](#upgrade)到 "企业版"。

## <a name="taxonomy"></a>分类 

下图演示了工作区的分类：

[![工作区分类](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

此图显示了工作区的以下组件：

+ 工作区可以包含[Azure 机器学习计算实例](concept-compute-instance.md)、使用运行 Azure 机器学习所需的 Python 环境配置的云资源。

+ [用户角色](how-to-assign-roles.md)使你可以与其他用户、团队或项目共享你的工作区。
+ [计算目标](concept-azure-machine-learning-architecture.md#compute-targets)用于运行试验。
+ 创建工作区时，还会为您创建[关联的资源](#resources)。
+ [试验](concept-azure-machine-learning-architecture.md#experiments)是用于构建模型的训练运行。  
+ [管道](concept-azure-machine-learning-architecture.md#ml-pipelines)是可重复使用的工作流，用于定型和重新训练模型。
+ [数据集](concept-azure-machine-learning-architecture.md#datasets-and-datastores)有助于管理用于模型定型和管道创建的数据。
+ 如果有要部署的模型，则需创建一个已注册的模型。
+ 使用已注册的模型和评分脚本来创建[部署终结点](concept-azure-machine-learning-architecture.md#endpoints)。

## <a name="tools-for-workspace-interaction"></a>用于工作区交互的工具

可以通过以下方式与工作区交互：

+ 在 web 上：
    + [Azure 机器学习工作室](https://ml.azure.com) 
    + [Azure 机器学习设计器](concept-designer.md)-仅适用于[Enterprise edition](overview-what-is-azure-ml.md#sku)工作区。
+ [适用于 python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)的任何 Python 环境。
+ 在具有适用于 R 的[AZURE 机器学习 SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)的任何 R 环境中。
+ 在命令行上使用 Azure 机器学习[CLI 扩展](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>使用工作区进行机器学习

机器学习任务读取和/或写入您的工作区中的项目。

+ 运行试验，将模型写入试验运行结果定型到工作区。
+ 使用自动 ML 来训练模型-将定型结果写入工作区。
+ 在工作区中注册模型。
+ 部署模型-使用注册的模型来创建部署。
+ 创建并运行可重用工作流。
+ 查看机器学习项目，如试验、管道、模型和部署。
+ 跟踪和监视模型。

## <a name="workspace-management"></a>工作区管理

你还可以执行以下工作区管理任务：

| 工作区管理任务   | 门户              | 工作室 | Python SDK/R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| 创建工作区        | **&check;**     | | **&check;** | **&check;** |
| 管理工作区访问    | **&check;**   || |  **&check;**    |
| 升级到 Enterprise edition    | **&check;** | **&check;**  | |     |
| 创建和管理计算资源    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| 创建笔记本 VM |   | **&check;** | |     |

> [!NOTE]
> 计算实例仅适用于区域为“美国中北部”或“英国南部”的工作区。
>如果你的工作区在任何其他区域，则可以继续创建并使用[笔记本 VM](concept-compute-instance.md#notebookvm)。

## <a name='create-workspace'></a>创建工作区

创建工作区时，可以决定是使用[基本版还是企业版](overview-what-is-azure-ml.md#sku)来创建工作区。 版本确定工作区中可用的功能。 企业版中的其他功能使你能够访问[Azure 机器学习设计器](concept-designer.md)，以及生成[自动化机器学习试验](tutorial-first-experiment-automated-ml.md)的工作室版本。  有关详细信息和定价信息，请参阅[Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。

有多种方法可以创建工作区：  

* 使用 " [Azure 门户](how-to-manage-workspace.md)" 单击界面，逐步完成每个步骤。
* 使用用于[python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)从 Python 脚本或木星笔记本动态创建工作区
* 需要使用企业安全标准自动执行或自定义创建时，请使用[Azure 资源管理器模板](how-to-create-workspace-template.md)或[Azure 机器学习 CLI](reference-azure-machine-learning-cli.md) 。
* 如果使用 Visual Studio Code，请使用[VS Code 扩展](tutorial-setup-vscode-extension.md)。

## <a name="upgrade"></a>升级到 Enterprise edition

你可以使用 Azure 门户将[工作区从 Basic 升级到 Enterprise edition](how-to-manage-workspace.md#upgrade) 。 不能将 Enterprise edition 工作区降级为基本版工作区。 

## <a name="resources"></a>关联的资源

创建新工作区时，它会自动创建工作区使用的几个 Azure 资源：

+ [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)：注册在训练和部署模型时使用的 docker 容器。 若要最大程度地降低成本，请在创建部署映像之前**延迟加载**ACR。
+ [Azure 存储帐户](https://azure.microsoft.com/services/storage/)：用作工作区的默认数据存储。  与 Azure 机器学习计算实例一起使用的 Jupyter 笔记本也存储在此处。
+ [Azure 应用程序 Insights](https://azure.microsoft.com/services/application-insights/)：存储有关模型的监视信息。
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)：存储工作区所需的计算目标和其他敏感信息所使用的机密。

> [!NOTE]
> 除创建新版本以外，还可以使用现有的 Azure 服务。

## <a name="next-steps"></a>后续步骤

若要开始 Azure 机器学习，请参阅：

+ [Azure 机器学习概述](overview-what-is-azure-ml.md)
+ [创建工作区](how-to-manage-workspace.md)
+ [管理工作区](how-to-manage-workspace.md)
+ [教程：通过 Python SDK 开始创建你的首个 ML 试验](tutorial-1st-experiment-sdk-setup.md)
+ [教程：通过 R SDK 开始 Azure 机器学习](tutorial-1st-r-experiment.md)
+ [教程：使用自动机器学习创建您的第一个分类模型](tutorial-first-experiment-automated-ml.md)（仅适用于[企业版](overview-what-is-azure-ml.md#sku)工作区）
+ [教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)（仅适用于[企业版](overview-what-is-azure-ml.md#sku)工作区）
