---
title: Azure 机器学习中的数据
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何安全地连接到数据，并将这些数据用于机器学习任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128497"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure 机器学习中的数据访问

Azure 机器学习使连接到云中的数据变得容易。  它在基础存储服务上提供了一个抽象层，因此您可以安全地访问和使用数据，而无需编写特定于存储类型的代码。 Azure 机器学习还提供以下数据功能：

*    数据血统的个版本和跟踪
*    数据标记 
*    数据偏差监视
*    与熊猫和火花数据帧的互操作性

## <a name="data-workflow"></a>数据工作流

当您准备好在基于云的存储解决方案中使用数据时，我们建议使用以下数据交付工作流。 此工作流假定 Azure 中的基于云的存储服务中具有[Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)和数据。 

1. 创建[Azure 机器学习数据存储](#datastores)以存储到 Azure 存储的连接信息。

2. 从该数据存储中，创建[Azure 机器学习数据集](#datasets)以指向基础存储中的特定文件。 

3. 要在机器学习实验中使用该数据集，可以
    1. 将其安装到实验的计算目标中，用于模型训练。

        **或** 

    1. 直接在 Azure 机器学习解决方案中使用它，例如自动机器学习 （自动 ML） 实验运行、机器学习管道或 Azure[机器学习设计器](concept-designer.md)。

4. 为模型输出数据集创建[数据集监视器](#data-drift)，以检测数据漂移。 

5. 如果检测到数据偏移，请更新输入数据集，并相应地重新训练模型。

下图提供了此建议的工作流的可视化演示。

![Data-concept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>数据存储

Azure 机器学习数据存储安全地将连接信息保留到 Azure 存储，因此无需在脚本中对其进行编码。 [注册并创建数据存储](how-to-access-data.md)，以便轻松连接到存储帐户，并访问基础 Azure 存储服务中的数据。 

Azure 中支持的基于云的存储服务可以注册为数据存储：

+ Azure Blob 容器
+ Azure 文件共享
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL 数据库
+ Azure Database for PostgreSQL
+ Databricks 文件系统
+ Azure Database for MySQL

## <a name="datasets"></a>数据集

Azure 机器学习数据集是指向存储服务中数据的引用。 它们不是您的数据副本，因此不会产生额外的存储成本。 要与存储中的数据进行交互，[请创建数据集](how-to-create-register-datasets.md)，将数据打包到用于机器学习任务的可消耗对象中。 将数据集注册到工作区可在不同的试验中共享和重用该数据集，而不会造成数据引入复杂性。

数据集可以通过数据存储从本地文件、公共 URL、Azure[开放数据集](https://azure.microsoft.com/services/open-datasets/)或 Azure 存储服务创建。 要从内存中的熊猫数据框创建数据集，请将数据写入本地文件（如镶木地板）并从该文件创建数据集。  

我们支持两种类型的数据集： 
+ [表格数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通过分析提供的文件或文件列表来表示表格格式的数据。 您可以将表格数据集加载到熊猫或 Spark 数据帧中，以便进一步操作和清理。 有关可以从中创建表格数据集的完整数据格式列表，请参阅[表格数据集工厂类](https://aka.ms/tabulardataset-api-reference)。

+ [文件数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)引用数据存储或公共 URL 中的单个或多个文件。 您可以将文件数据集引用[的文件下载或装载](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)到计算目标。

可在以下文档中找到更多数据集功能：

+ 对数据集世系进行[版本控制和跟踪](how-to-version-track-datasets.md)。
+ [监视数据集](how-to-monitor-datasets.md)以帮助检测数据偏移。    

## <a name="work-with-your-data"></a>使用数据

使用数据集可以通过与 Azure 机器学习功能的无缝集成来完成许多机器学习任务。 

+ 创建[数据标记项目](#label)。
+ 培训机器学习模型：
     + [自动化 ML 试验](how-to-use-automated-ml-for-ml-models.md)
     + [设计器](tutorial-designer-automobile-price-train-score.md#import-data)
     + [笔记本](how-to-train-with-datasets.md)
     + [Azure 机器学习管道](how-to-create-your-first-pipeline.md)
+ 访问数据集，用于在[机器学习管道](how-to-create-your-first-pipeline.md)中进行[批处理推理](how-to-use-parallel-run-step.md)评分。
+ 设置数据集监视器以检测[数据偏移](#drift)。

<a name="label"></a>

## <a name="data-labeling"></a>数据标记

在机器学习项目中，标记大量数据通常是一件很麻烦的事情。 这些包含计算机视觉组件（如图像分类或对象检测）的项目通常需要数千个图像和对应的标签。

Azure 机器学习提供了一个中心位置，用于创建、管理和监视标签项目。 标记项目有助于协调数据、标签和团队成员，使你能够更有效地管理标记任务。 当前支持的任务包括图像分类（多标签或多类）以及使用边界框的对象标识。

创建[数据标记项目](how-to-create-labeling-projects.md)，并输出可在机器学习试验中使用的数据集。

<a name="drift"></a>

## <a name="data-drift"></a>数据偏移

在机器学习的上下文中，数据偏移是指模型输入数据中导致模型性能下降的变化。 这是模型准确度在一段时间后下降的最主要原因之一，因此，监视数据偏移有助于检测模型性能问题。

请参阅[创建数据集监视器](how-to-monitor-datasets.md)一文来详细了解如何检测数据集中新数据的数据偏移并发出警报。

## <a name="next-steps"></a>后续步骤 

+ [使用以下步骤](how-to-create-register-datasets.md)在 Azure 机器学习工作室或使用 Python SDK 创建数据集。
+ 阅读[示例笔记本](https://aka.ms/dataset-tutorial)中的数据集训练示例。
+ 有关数据偏移的示例，请参阅此[数据偏移教程](https://aka.ms/datadrift-notebook)。
