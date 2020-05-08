---
title: 数据引入 & 自动化
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
ms.openlocfilehash: 475c4fd6b34996c83035c4f7ef93b9fa02ded11f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789855"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Azure 机器学习工作流的数据引入选项

本文介绍 Azure 机器学习提供的数据引入选项的优点和缺点。 

选择：
+ [Azure 数据工厂](#azure-data-factory)管道，专门构建以提取、加载和转换数据

+ [Azure 机器学习 PYTHON SDK](#azure-machine-learning-python-sdk)，为基本数据引入任务提供自定义代码解决方案。

+ 两者的组合

数据引入是指将非结构化数据从一个或多个源中提取出来，然后准备好用于训练机器学习模型的过程。 这也会耗费时间，尤其是当手动完成时，如果有大量的数据来自多个源。 自动执行此工作可释放资源并确保模型使用最新的适用数据。

## <a name="azure-data-factory"></a>Azure 数据工厂

[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)原生支持数据引入管道的数据源监视和触发器。  

下表汇总了对数据引入工作流使用 Azure 数据工厂的优点和缺点。

|优点|缺点
---|---
专门设计用于提取、加载和转换数据。|目前提供有限的一组 Azure 数据工厂管道任务 
可以创建数据驱动式工作流用于协调大规模的数据移动与转换。|生成和维护成本高昂。 有关详细信息，请参阅 Azure 数据工厂的[定价页](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)。
与 [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) 和 [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) 等各种 Azure 工具集成 | 原生不能运行脚本，而是依赖独立的计算资源来运行脚本 
原生支持数据源触发的数据引入| 
数据准备和模型训练过程是独立的。|
Azure 数据工厂数据流的嵌入式数据世系功能|
为非脚本方法提供低代码体验[用户界面](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) |

以下步骤和下图演示了 Azure 数据工厂的数据引入工作流。

1. 从源提取数据
1. 转换数据，并将其保存到充当 Azure 机器学习数据存储的输出 Blob 容器
1. 存储准备好的数据后，Azure 数据工厂管道将调用一个训练机器学习管道，用于接收已准备好用于训练模型的数据


    ![ADF 数据引入](media/concept-data-ingestion/data-ingest-option-one.svg)
    
了解如何使用 [Azure 数据工厂](how-to-data-ingest-adf.md)为机器学习生成数据引入管道。

## <a name="azure-machine-learning-python-sdk"></a>Azure 机器学习 Python SDK 

使用 [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml) 可将数据引入任务合并到 [Azure 机器学习管道](how-to-create-your-first-pipeline.md)步骤中。

下表汇总了对数据引入任务使用 SDK 和 ML 管道步骤的优点和缺点。

优点| 缺点
---|---
配置自己的 Python 脚本 | 原生不支持数据源更改触发。 需要实现逻辑应用或 Azure 函数
在每次执行模型训练的过程中准备数据|需要拥有创建数据引入脚本方面的开发技能
支持对各种计算目标（包括 [Azure 机器学习计算](concept-compute-target.md#azure-machine-learning-compute-managed)）使用数据准备脚本 |不提供用于创建引入机制的用户界面

在下图中，Azure 机器学习管道由两个步骤组成：数据引入和模型训练。 数据引入步骤包含可以使用 Python 库和 Python SDK 完成的任务（例如从本地/Web 源提取数据），以及基本数据转换（例如缺失值插补）。 然后，训练步骤使用准备好的数据作为训练脚本的输入来训练机器学习模型。 

![Azure 管道 + SDK 数据引入](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>后续步骤

请参阅以下操作指南文章：
* [使用 Azure 数据工厂构建数据引入管道](how-to-data-ingest-adf.md)

* [利用 Azure Pipelines 自动执行和管理数据引入管道](how-to-cicd-data-ingestion.md)。
