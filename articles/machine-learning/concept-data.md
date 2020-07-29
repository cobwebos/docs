---
title: 云中的安全数据访问
titleSuffix: Azure Machine Learning
description: 了解如何从 Azure 机器学习安全地连接到数据，以及如何使用数据集和数据存储来执行 ML 任务。 数据存储可以存储来自 Azure Blob、Azure Data Lake Gen 1 和 Azure Data Lake Gen 2、SQL DB、Databricks 等的数据
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.custom: tracking-python
ms.openlocfilehash: 15cf4aa6adda26991e76ec8a5e7378766fe2a21f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84552644"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure 机器学习中的安全数据访问

Azure 机器学习使你可以轻松连接到云中的数据。  它在基础存储服务上提供抽象层，让你可以安全地访问和使用数据，而无需编写特定于存储类型的代码。 Azure 机器学习还提供了以下数据功能：

*    数据世系的版本控制和跟踪
*    数据标记 
*    数据偏差监视
*    与 Pandas 和 Spark DataFrames 的互操作性

## <a name="data-workflow"></a>数据工作流

准备好在基于云的存储解决方案中使用数据时，建议使用以下数据发送工作流。 此工作流假设你拥有 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，并在 Azure 中的基于云的存储服务中拥有数据。 

1. 创建 [Azure 机器学习数据存储](#datastores)以将连接信息存储到 Azure 存储。

2. 在该数据存储中，创建一个 [Azure 机器学习数据集](#datasets)以指向基础存储中的特定文件。 

3. 若要在机器学习试验中使用该数据集，可采用以下方法
    1. 将该数据集装载到试验的计算目标以进行模型训练。

        **或者** 

    1. 在自动化机器学习（自动化 ML）试验运行、机器学习管道或 [Azure 机器学习设计器](concept-designer.md)等 Azure 机器学习解决方案中直接使用该数据集。

4. 针对模型输出数据集创建[数据集监视器](#data-drift)以检测数据偏移。 

5. 如果检测到数据偏移，请更新输入数据集，并相应地重新训练模型。

下图提供了此建议的工作流的直观演示。

![Data-concept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>数据存储

Azure 机器学习数据存储可安全地将连接信息保存到 Azure 存储中，因此无需在脚本中对其进行编码。 [注册并创建一个数据存储](how-to-access-data.md)即可轻松连接到存储帐户，并访问底层 Azure 存储服务中的数据。 

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

Azure 机器学习数据集是指向存储服务中数据的引用。 它们不是你数据的副本，因此不会产生额外的存储成本。 若要与存储中的数据进行交互，请[创建一个数据集](how-to-create-register-datasets.md)以将数据打包成机器学习任务可用的对象。 将数据集注册到工作区可在不同的试验中共享和重用该数据集，而不会造成数据引入复杂性。

可以从本地文件、公共 URL、[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)或经由数据存储的 Azure 存储服务创建数据集。 若要从内存中 pandas 数据帧创建数据集，请将数据写入本地文件（例如 parquet），然后从该文件创建数据集。  

我们支持 2 种类型的数据集： 
+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 通过分析提供的文件或文件列表来以表格格式表示数据。 可以将 TabularDataset 加载到 Pandas 或 Spark DataFrame 中，以便进一步处理和清理。 有关可从中创建 TabularDataset 的数据格式的完整列表，请参阅 [TabularDatasetFactory 类](https://aka.ms/tabulardataset-api-reference)。

+ [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 引用数据存储或公共 URL 中的单个或多个文件。 可以将 FileDatasets 引用的文件[下载或装载](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)到计算目标。

可在以下文档中找到更多数据集功能：

+ 对数据集世系进行[版本控制和跟踪](how-to-version-track-datasets.md)。
+ [监视数据集](how-to-monitor-datasets.md)以帮助检测数据偏移。    

## <a name="work-with-your-data"></a>使用数据

使用数据集可以通过与 Azure 机器学习功能的无缝集成来完成许多机器学习任务。 

+ 创建[数据标记项目](#label)。
+ 训练机器学习模型：
     + [自动化 ML 试验](how-to-use-automated-ml-for-ml-models.md)
     + [设计器](tutorial-designer-automobile-price-train-score.md#import-data)
     + [笔记本](how-to-train-with-datasets.md)
     + [Azure 机器学习管道](how-to-create-your-first-pipeline.md)
+ 访问数据集，以使用[机器学习管道](how-to-create-your-first-pipeline.md)中的[批量推理](how-to-use-parallel-run-step.md)进行评分。
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

+ 若要在 Azure 机器学习工作室或 Python SDK 中创建数据集，请[使用这些步骤](how-to-create-register-datasets.md)。
+ 阅读[示例笔记本](https://aka.ms/dataset-tutorial)中的数据集训练示例。
+ 有关数据偏移的示例，请参阅此[数据偏移教程](https://aka.ms/datadrift-notebook)。
