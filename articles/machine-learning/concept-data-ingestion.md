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
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086887"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Azure 机器学习中的数据引入

在本文中，您将了解 Azure 机器学习中可用的以下数据引入选项的优缺点。 

1. [Azure 数据工厂](#use-azure-data-factory)管道
2. [Azure 机器学习 Python SDK](#use-the-python-sdk)

数据引入是从一个或多个来源提取非结构化数据，然后为训练机器学习模型做好准备的过程。 这还非常耗时，尤其是在手动完成时，以及如果您有来自多个源的大量数据。 自动化此工作可释放资源，并确保模型使用最新和适用的数据。

Azure 数据工厂 （ADF） 是专门为提取、加载和转换数据而构建的，但是 Python SDK 允许您为基本数据引入任务开发自定义代码解决方案。 如果这两者都不是您需要的，您也可以一起使用 ADF 和 Python SDK 来创建满足您需求的整体数据引入工作流。 

## <a name="use-azure-data-factory"></a>使用 Azure 数据工厂

[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)为数据引入管道的数据源监视和触发器提供本机支持。  

下表总结了使用 Azure 数据工厂进行数据引入工作流的优缺点。

|优点|缺点
---|---
专为提取、加载和转换数据而构建。|当前提供有限的 Azure 数据工厂管道任务集 
允许您创建数据驱动的工作流，以便大规模协调数据移动和转换。|建造和维护费用高昂。 有关详细信息，请参阅 Azure 数据工厂的[定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)。
与[Azure 数据块](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)和 Azure[函数](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)等各种 Azure 工具集成 | 不本机运行脚本，而是依赖于脚本运行的单独计算 
本机支持数据源触发的数据引入| 
数据准备和模型培训过程是分开的。|
Azure 数据工厂数据流的嵌入式数据沿线功能|
[为非](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)脚本方法提供低代码体验用户界面 |

这些步骤和下图说明了 Azure 数据工厂的数据引入工作流。

1. 从源中提取数据
1. 将数据转换并保存到输出 Blob 容器，该容器用作 Azure 机器学习的数据存储
1. 存储准备好的数据后，Azure 数据工厂管道将调用一个培训机器学习管道，该管道接收用于模型训练的已准备好数据


    ![ADF 数据引入](media/concept-data-ingestion/data-ingest-option-one.svg)
    
了解如何使用[Azure 数据工厂](how-to-data-ingest-adf.md)为机器学习构建数据引入管道。

## <a name="use-the-python-sdk"></a>使用 Python SDK 

使用[Python SDK，](https://docs.microsoft.com/python/api/overview/azure/ml)可以将数据引入任务合并到 Azure[机器学习管道](how-to-create-your-first-pipeline.md)步骤中。

下表总结了使用 SDK 和 ML 管道步骤执行数据引入任务的优点和缺点。

优点| 缺点
---|---
配置您自己的 Python 脚本 | 本机不支持数据源更改触发。 需要逻辑应用或 Azure 函数实现
作为每个模型培训执行的一部分的数据准备|创建数据引入脚本需要开发技能
支持各种计算目标的数据准备脚本，包括[Azure 机器学习计算](concept-compute-target.md#azure-machine-learning-compute-managed) |不提供用于创建引入机制的用户界面

在下图中，Azure 机器学习管道由两个步骤组成：数据引入和模型培训。 数据引入步骤包含可以使用 Python 库和 Python SDK 完成的任务，例如从本地/Web 源中提取数据以及基本数据转换（如缺少值归因）。 然后，培训步骤使用准备好的数据作为训练脚本的输入来训练机器学习模型。 

![Azure 管道 + SDK 数据引入](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>后续步骤

* 了解如何使用[Azure 数据工厂](how-to-data-ingest-adf.md)构建机器学习的数据引入管道
* 了解如何使用[Azure 管道](how-to-cicd-data-ingestion.md)自动管理数据引入管道的开发生命周期。
