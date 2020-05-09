---
title: 保护云中的数据访问
titleSuffix: Azure Machine Learning
description: 了解如何从 Azure 机器学习安全地连接到您的数据，以及如何使用数据集和数据存储来执行 ML 任务。 数据存储可以存储 Azure Blob 中的数据，Azure Data Lake 第 1 & 2、SQL db、Databricks,.。。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.openlocfilehash: e41d00a31a0065e2923259cbb0bb36986cbb2ec8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995068"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure 机器学习中的安全数据访问

Azure 机器学习可以轻松地连接到云中的数据。  它通过基础存储服务提供抽象层，因此可以安全地访问和处理数据，而无需编写特定于存储类型的代码。 Azure 机器学习还提供了以下数据功能：

*    数据沿袭的版本控制和跟踪
*    数据标记 
*    数据偏差监视
*    与 Pandas 和 Spark DataFrames 的互操作性

## <a name="data-workflow"></a>数据工作流

准备好在基于云的存储解决方案中使用数据时，我们建议使用以下数据传递工作流。 此工作流假设你在 Azure 中的基于云的存储服务中有[azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)和数据。 

1. 创建[Azure 机器学习数据](#datastores)存储，以便将连接信息存储到 Azure 存储。

2. 在该数据存储中，创建一个[Azure 机器学习数据集](#datasets)，以指向基础存储中的特定文件。 

3. 若要在机器学习试验中使用该数据集，可以
    1. 将其装载到试验的计算目标，以实现模型定型。

        **或** 

    1. 直接在 Azure 机器学习的解决方案中使用它，例如自动机器学习（自动 ML）试验运行、机器学习管道或[Azure 机器学习设计器](concept-designer.md)。

4. 创建模型输出数据集的[数据集监视器](#data-drift)以检测数据偏移。 

5. 如果检测到数据偏移，请更新输入数据集，并相应地重新训练模型。

下图提供了此建议的工作流的直观演示。

![Data-concept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>数据存储

Azure 机器学习数据存储安全地将连接信息保存到 Azure 存储中，因此无需在脚本中对其进行编码。 [注册并创建一个数据](how-to-access-data.md)存储，以便轻松连接到存储帐户，并访问基础 Azure 存储服务中的数据。 

Azure 中支持的基于云的存储服务，可注册为数据存储：

+ Azure Blob 容器
+ Azure 文件共享
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL 数据库
+ Azure Database for PostgreSQL
+ Databricks 文件系统
+ Azure Database for MySQL

## <a name="datasets"></a>数据集

Azure 机器学习数据集是指向存储服务中的数据的引用。 它们不是你的数据的副本，因此不会产生额外的存储成本。 若要与存储中的数据进行交互，请[创建一个数据集](how-to-create-register-datasets.md)，将数据打包到可用于机器学习任务的可耗用对象。 将数据集注册到工作区可在不同的试验中共享和重用该数据集，而不会造成数据引入复杂性。

可以通过数据存储从本地文件、公共 url、 [Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)或 azure 存储服务创建数据集。 若要在内存 pandas 数据帧中创建数据集，请将数据写入本地文件（如 parquet），并从该文件创建数据集。  

我们支持2种类型的数据集： 
+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通过分析提供的文件或文件列表，以表格格式表示数据。 可以将 TabularDataset 加载到 Pandas 或 Spark 数据帧，以便进一步操作和清理。 有关可从创建 TabularDatasets 的数据格式的完整列表，请参阅[TabularDatasetFactory 类](https://aka.ms/tabulardataset-api-reference)。

+ [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)引用数据存储或公用 url 中的单个或多个文件。 你可以将 FileDatasets 引用的[文件下载或装载](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)到你的计算目标。

可在以下文档中找到更多数据集功能：

+ 对数据集世系进行[版本控制和跟踪](how-to-version-track-datasets.md)。
+ [监视数据集](how-to-monitor-datasets.md)以帮助检测数据偏移。    

## <a name="work-with-your-data"></a>使用数据

使用数据集可以通过与 Azure 机器学习功能的无缝集成来完成许多机器学习任务。 

+ 创建[数据标记项目](#label)。
+ 训练机器学习模型：
     + [自动化 ML 试验](how-to-use-automated-ml-for-ml-models.md)
     + [设计器](tutorial-designer-automobile-price-train-score.md#import-data)
     + [记事本](how-to-train-with-datasets.md)
     + [Azure 机器学习管道](how-to-create-your-first-pipeline.md)
+ 使用[机器学习管道](how-to-create-your-first-pipeline.md)中的[批处理推理](how-to-use-parallel-run-step.md)访问数据集。
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

+ [使用以下步骤](how-to-create-register-datasets.md)在 Azure 机器学习 studio 或使用 Python SDK 创建数据集。
+ 阅读[示例笔记本](https://aka.ms/dataset-tutorial)中的数据集训练示例。
+ 有关数据偏移的示例，请参阅此[数据偏移教程](https://aka.ms/datadrift-notebook)。
