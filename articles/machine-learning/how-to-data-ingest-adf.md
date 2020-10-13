---
title: 使用 Azure 数据工厂进行数据引入
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 数据工厂生成数据引入管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ad04566699b2eebb0cbd7a9f242de38bc75e2015
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986402"
---
# <a name="data-ingestion-with-azure-data-factory"></a>使用 Azure 数据工厂进行数据引入

本文介绍如何使用 Azure 数据工厂 (ADF) 生成数据引入管道。 此管道用于引入要在 Azure 机器学习中使用的数据。 Azure 数据工厂可让你轻松提取、转换和加载 (ETL) 数据。 转换数据并将其载入存储后，可以使用这些数据来训练机器学习模型。

可以使用本机 ADF 活动和[数据流](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)等检测工具来处理简单的数据转换。 涉及到较复杂的方案时，可以使用一些自定义代码来处理数据。 例如 Python 或 R 代码。

在引入期间，可以通过多种常用方法使用 Azure 数据工厂来转换数据。 每种方法有各自的优缺点，这也决定了它是否适合特定的用例：

| 方法 | 优点 | 缺点 |
| ----- | ----- | ----- |
| ADF + Azure Functions | 低延迟，无服务器计算</br>有状态函数</br>可重用函数 | 仅适用于短时间运行的处理 |
| ADF + 自定义组件 | 大规模并行计算</br>适合用于繁重算法 | 将代码包装到可执行文件中</br>处理依赖项和 IO 时存在的复杂性 |
| ADF + Azure Databricks 笔记本 | Apache Spark</br>本机 Python 环境 | 开销可能很高</br>初次创建群集时需要较长时间并会增大延迟

## <a name="adf-with-azure-functions"></a>ADF 与 Azure Functions 相结合

![关系图显示了 azure 数据工厂管道、Azure 函数和运行 M L 管道，以及使用定型模型的 Azure 机器学习管道，以及如何与原始数据和准备的数据进行交互。](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions 允许运行小段代码（函数），且不需要担心应用程序基础结构。 使用此选项时，数据将通过包装在 Azure 函数中的自定义 Python 代码进行处理。 

该函数是使用 [ADF Azure 函数活动](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)调用的。 此方法非常适合轻型数据转换。 

* 优点：
    * 以相对较低的延迟在无服务器计算资源中处理数据
    * ADF 管道可以调用一个[持久性 Azure 函数](/azure/azure-functions/durable/durable-functions-overview)，该函数可实现复杂的数据转换流 
    * 可重复使用且可从其他位置调用的 Azure 函数会抽象掉数据转换的详细信息
* 缺点：
    * 在与 ADF 结合使用之前，必须先创建 Azure Functions
    * Azure Functions 仅适用于短时间运行的数据处理

## <a name="adf-with-custom-component-activity"></a>ADF 与自定义组件活动相结合

![关系图显示了一个 Azure 数据工厂管道，其中包含一个自定义组件并运行 M L 管道，以及一个带有定型模型的 Azure 机器学习管道，以及如何与原始数据和准备好的数据进行交互。](media/how-to-data-ingest-adf/adf-customcomponent.png)

使用此选项时，数据将通过包装在可执行文件中的自定义 Python 代码进行处理。 该可执行文件是使用 [ADF 自定义组件活动](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)调用的。 与前面的方法相比，此方法更适合较大的数据。

* 优点：
    * 数据将在 [Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) 池中进行处理，该池提供大规模并行和高性能计算
    * 可用于运行繁重的算法并处理大量数据
* 缺点：
    * 在与 ADF 结合使用之前，必须先创建 Azure Batch 池
    * 将 Python 代码包装到可执行文件会产生过多的工程工作量。 处理依赖项和输入/输出参数时存在的复杂性

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF 与 Azure Databricks Python 笔记本相结合

![关系图显示了一个 Azure 数据工厂管道，其中包含 Azure Databricks Python 和运行 M L 管道，以及一个带有定型模型的 Azure 机器学习管道，以及如何与原始数据和准备好的数据进行交互。](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是 Microsoft 云中基于 Apache Spark 的分析平台。

使用此方法时，数据转换将由 Azure Databricks 群集上运行的某个 [Python 笔记本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)执行。 这也许是全面利用 Azure Databricks 服务的强大之处的最常见方法。 它旨在用于大规模的分布式数据处理。

* 优点：
    * 在以 Apache Spark 环境为后盾的最强大数据处理 Azure 服务中转换数据
    * 原生支持 Python 以及数据科学框架和库（包括 TensorFlow、PyTorch 和 scikit-learn）
    * 无需将 Python 代码包装到函数或可执行模块中。 代码按原样运行。
* 缺点：
    * 在与 ADF 结合使用之前，必须先创建 Azure Databricks 基础结构
    * 开销可能很高，具体取决于 Azure Databricks 配置
    * 从“冷”模式运转计算群集需要一段时间，使解决方案遇到较高的延迟 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>在 Azure 机器学习管道中使用数据

![关系图显示了一个 Azure 数据工厂管道和一个 Azure 机器学习管道，以及它们如何与原始数据和准备的数据进行交互。 数据工厂管道将数据馈送到准备好的数据数据库，该数据库将数据存储在机器学习工作区中馈送数据集。](media/how-to-data-ingest-adf/aml-dataset.png)

从 ADF 管道转换的数据将保存到数据存储 (如 Azure Blob) 。 Azure 机器学习可以使用 [数据存储](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) 和 [数据集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)访问此数据。

每次运行 ADF 管道时，数据将保存到存储中的不同位置。 若要将位置传递到 Azure 机器学习，ADF 管道将调用 Azure 机器学习管道。 调用 ML 管道时，数据位置和运行 ID 作为参数发送。 然后，ML 管道可以使用数据位置创建数据存储/数据集。 

> [!TIP]
> 数据集 [支持版本控制](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)，因此 ML 管道可以注册一个新版本的数据集，该数据集指向 ADF 管道中的最新数据。

数据可通过数据存储或数据集访问后，可以使用它来训练 ML 模型。 训练过程可能是从 ADF 中调用的同一 ML 管道的一部分。 或者，它可能是一个单独的进程，如 Jupyter 笔记本中的试验。

由于数据集支持版本控制，每次从管道运行时都会创建一个新版本，因此很容易理解哪个版本的数据用于训练模型。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据工厂中运行 Databricks 笔记本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Azure 机器学习中的数据集定型模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [数据引入管道的 DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

