---
title: Azure 机器学习中的数据
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何与你的数据进行交互，以及如何在机器学习试验中使用数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4ea0f7092862ef910e9f44e81cc4f5f40849bc70
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822950"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure 机器学习中的数据访问

本文介绍了 Azure 机器学习的数据管理和用于机器学习任务的集成解决方案。 本文假设已创建[azure 存储帐户](https://docs.microsoft.comazure/storage/common/storage-quickstart-create-account?tabs=azure-portal)和[azure 存储服务](https://docs.microsoft.com/azure/storage/common/storage-introduction)。

准备好在存储中使用数据时，我们建议你

1. 创建 Azure 机器学习数据存储。
2. 在该数据存储中，创建 Azure 机器学习数据集。 
3. 在机器学习试验中使用该数据集，方法是： 
    1. 将其装载到试验的计算目标以便进行模型定型

        **或** 

    1. 直接在 Azure 机器学习的解决方案中使用它，例如自动机器学习（自动 ML）试验、机器学习管道和[Azure 机器学习设计器](concept-designer.md)。
4. 为模型输入和输出数据集创建数据集监视器，以检测数据偏移。 
5. 如果检测到数据偏移，请更新数据集，并相应地重新训练模型。

下图提供了此推荐的数据访问工作流的直观演示。

![数据概念-关系图](media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>访问存储中的数据

若要访问你的存储帐户中的数据，Azure 机器学习提供数据存储和数据集。 数据存储提供存储服务的抽象层。 由于连接信息保存在数据存储中，而不是在脚本中公开的，因此这有助于安全性和轻松访问你的存储。 数据集指向您要用于机器学习试验的基础存储中的特定文件。 数据存储和数据集一起为你的机器学习任务提供安全、可缩放且可重复的数据交付工作流。

### <a name="datastores"></a>数据存储

Azure 机器学习数据存储是通过 Azure 存储服务进行的存储提取。 [注册并创建一个数据存储](how-to-access-data.md)，以便轻松连接到 azure 存储帐户，并访问基础 Azure 存储服务中的数据。

可注册为数据存储的受支持 Azure 存储服务：
+ Azure Blob 容器
+ Azure 文件共享
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks 文件系统
+ Azure Database for MySQL

### <a name="datasets"></a>数据集

[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)以与数据存储中的数据进行交互，并将数据打包到可用于机器学习任务的可耗用对象。 将数据集注册到你的工作区，以便在不使用数据引入复杂度的情况下跨不同试验共享和重用它。

可以从本地文件、公共 url、 [Azure 开放数据集](#open)或数据存储中的特定文件创建数据集。 若要在内存 pandas 数据帧中创建数据集，请将数据写入本地文件（例如 csv），并从该文件创建数据集。 数据集不是你的数据的副本，而是指向你的存储服务中的数据的引用，因此不会产生额外的存储成本。 

下图显示，如果你没有 Azure 存储服务，则可以直接从本地文件、公共 url 或 Azure 开放数据集创建数据集。 这样做会将你的数据集连接到默认的数据存储，该数据存储是通过试验的[Azure 机器学习工作区](concept-workspace.md)自动创建的。

![数据概念-关系图](media/concept-data/dataset-workflow.svg)

可在以下文档中找到其他数据集功能：

+ [版本和跟踪](how-to-version-track-datasets.md)数据集沿袭。
+ [监视数据集](how-to-monitor-datasets.md)，以帮助进行数据偏移检测。
+  有关这两种类型的数据集的文档，请参阅以下内容：
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通过分析提供的文件或文件列表，以表格格式表示数据。 这使您可以将数据具体化为 Pandas 或 Spark 数据帧，以便进一步操作和清理。 有关可从创建 TabularDatasets 的文件的完整列表，请参阅[TabularDatasetFactory 类](https://aka.ms/tabulardataset-api-reference)。

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)引用数据存储或公用 url 中的单个或多个文件。 通过此方法，你可以将选择的文件作为 FileDataset 对象下载或装载到你的计算目标。

## <a name="work-with-your-data"></a>处理数据

使用数据集，您可以通过与 Azure 机器学习功能无缝集成来完成许多机器学习任务。 

+ [训练机器学习模型](how-to-train-with-datasets.md)。
+ 使用数据集 
     + [自动 ML 试验](how-to-create-portal-experiments.md)
     + [设计器](tutorial-designer-automobile-price-train-score.md#import-data) 
+ 使用[机器学习管道](how-to-create-your-first-pipeline.md)中的批处理推理访问数据集。
+ 创建[数据标记项目](#label)。
+ 为[数据偏移](#drift)检测设置数据集监视器。

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure 开放数据集

[Azure 开放数据集](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 打开的数据集位于 Microsoft Azure 云中，并集成到 Azure 机器学习中。 也可以通过 API 访问数据集，并在 Power BI 和 Azure 数据工厂等其他产品中使用它们。

Azure 开放式数据集包括天气、人口普查、假日、公共安全和位置的公共域数据，这些数据可帮助你训练机器学习模型和丰富预测解决方案。 还可以共享 Azure 开放数据集中的公共数据集。

<a name="label"></a>

## <a name="data-labeling"></a>数据标记

在机器学习项目中，标记大量数据通常是一件很麻烦的事情。 具有计算机视觉组件（如图像分类或对象检测）的人员通常需要数千个图像和对应的标签。

Azure 机器学习提供了一个中心位置，用于创建、管理和监视标签项目。 标记项目有助于协调数据、标签和团队成员，使你能够更有效地管理标记任务。 当前支持的任务包括图像分类（多标签或多类）以及使用边界框的对象标识。

+ 创建[数据标记项目](how-to-create-labeling-projects.md)，并输出要在机器学习试验中使用的数据集。

<a name="drift"></a>

## <a name="data-drift"></a>数据偏移

在机器学习的上下文中，数据偏移是模型输入数据中的更改，导致模型性能下降。 这是模型准确性在一段时间内的最主要原因之一，因此监视数据偏移可帮助检测模型性能问题。
若要详细了解如何检测和警报数据集中的新数据，请参阅[创建数据集监视](how-to-monitor-datasets.md)一文。

## <a name="next-steps"></a>后续步骤 

+ 在 Azure 机器学习 studio 或使用 Python SDK 创建数据集，请[使用以下步骤。](how-to-create-register-datasets.md)
+ 试用[示例笔记本](https://aka.ms/dataset-tutorial)中的数据集定型示例。
+ 有关数据偏移示例，请参阅此[数据偏移教程](https://aka.ms/datadrift-notebook)。