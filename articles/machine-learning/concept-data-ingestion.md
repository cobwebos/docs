---
title: 数据引入选项
titleSuffix: Azure Machine Learning
description: 了解用于训练机器学习模型的数据引入选项。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 3370fc72b45720e2aae5731623843a1bc498911c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652069"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Azure 机器学习中的数据引入

本文介绍 Azure 机器学习提供的以下数据引入选项的优点和缺点。 

1. [Azure 数据工厂](#use-azure-data-factory)管道
2. [Azure 机器学习 Python SDK](#use-the-python-sdk)

数据引入是指将非结构化数据从一个或多个源中提取出来，然后准备好用于训练机器学习模型的过程。 这也会耗费时间，尤其是当手动完成时，如果有大量的数据来自多个源。 自动执行此工作可释放资源并确保模型使用最新的适用数据。

建议最初使用 Azure 数据工厂（ADF）进行评估，因为它是专门为提取、加载和转换数据而构建的。 如果你无法使用 ADF 满足你的要求，则可以使用 Python SDK 开发自定义代码解决方案，或将 ADF 和 Python SDK 一起使用来创建可满足你的需求的总体数据引入工作流。

## <a name="use-azure-data-factory"></a>使用 Azure 数据工厂

[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)为数据引入管道提供对数据源监视和触发器的本机支持。  

下表总结了使用 Azure 数据工厂进行数据引入工作流的优点和缺点。

|优点|缺点
---|---
专门构建以提取、加载和转换数据。|目前提供一组有限的 Azure 数据工厂管道任务 
允许您创建数据驱动的工作流，用于大规模协调数据移动和转换。|构建和维护成本高昂。 有关详细信息，请参阅 Azure 数据工厂的[定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)。
与各种 Azure 工具（如[Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)和[Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) ）集成 | 不能以本机方式运行脚本，而是依赖于脚本运行的单独计算 
本机支持数据源触发的数据引入| 
数据准备和模型定型过程是独立的。|
Azure 数据工厂的嵌入数据沿袭功能数据流|
为非脚本方法提供低代码体验[用户界面](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) |

以下步骤和下图说明了 Azure 数据工厂的数据引入工作流。

1. 从数据源中提取数据
1. 转换数据并将其保存到输出 blob 容器，该容器用作 Azure 机器学习的数据存储
1. 存储已准备的数据后，Azure 数据工厂管道将调用一个定型机器学习管道，用于接收模型定型的已准备数据


    ![ADF 数据引入](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>使用 Python SDK 

使用[PYTHON SDK](https://docs.microsoft.com/python/api/overview/azureml-sdk/?view=azure-ml-py)，你可以将数据引入任务合并到[Azure 机器学习管道](how-to-create-your-first-pipeline.md)步骤中。

下表总结了使用 SDK 的专业人员和职业以及用于数据引入任务的 ML 管道步骤。

优点| 缺点
---|---
配置自己的 Python 脚本 | 本身不支持数据源更改触发。 需要逻辑应用或 Azure 函数实现
作为每个模型定型执行的一部分的数据准备|需要开发技能来创建数据引入脚本
支持针对各种计算目标的数据准备脚本，包括[Azure 机器学习计算](concept-compute-target.md#azure-machine-learning-compute-managed) |不提供用于创建引入机制的用户界面

在下图中，Azure 机器学习管道包含两个步骤：数据引入和模型定型。 数据引入步骤包含可使用 Python 库和 Python SDK 完成的任务，例如从本地/web 源提取数据，以及基本数据转换，如缺少值插补法。 然后，培训步骤使用准备好的数据作为训练脚本的输入来训练机器学习模型。 

![Azure 管道 + SDK 数据引入](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>后续步骤

* 了解如何通过[Azure Pipelines](how-to-cicd-data-ingestion.md)自动执行和管理数据引入管道的开发生命周期。