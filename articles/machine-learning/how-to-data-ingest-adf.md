---
title: 通过 Azure 数据工厂引入数据
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 数据工厂构建数据引入管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274783"
---
# <a name="data-ingestion-with-azure-data-factory"></a>通过 Azure 数据工厂引入数据

本文介绍如何使用 Azure 数据工厂（ADF）生成数据引入管道。 此管道用于引入用于 Azure 机器学习的数据。 Azure 数据工厂允许你轻松提取、转换和加载（ETL）数据。 转换数据并将数据加载到存储后，可以使用它来训练机器学习模型。

简单的数据转换可与本机 ADF 活动和乐器（[如数据流）一起处理。](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) 当涉及到更复杂的方案时，可以用一些自定义代码来处理数据。 例如，Python 或 R 代码。

使用 Azure 数据工厂在引入期间转换数据的常用方法有多种。 每种方法都有一些优点和缺点来确定它是否适合于特定用例：

| 方法 | 优点 | 缺点 |
| ----- | ----- | ----- |
| ADF + Azure Functions | 低延迟，无服务器计算</br>有状态函数</br>可重用函数 | 仅适用于短时间运行的处理 |
| ADF + 自定义组件 | 大规模并行计算</br>适用于繁重算法 | 将代码包装到可执行文件中</br>处理依赖项和 IO 的复杂性 |
| ADF + Azure Databricks 笔记本 | Apache Spark</br>本机 Python 环境 | 开销可能很高</br>最初创建群集会占用时间并增加延迟

## <a name="adf-with-azure-functions"></a>带 Azure 功能的 ADF

![adf-功能](media/how-to-data-ingest-adf/adf-function.png)

Azure Functions 允许你运行少量的代码（函数），而无需担心应用程序基础结构。 在此选项中，使用包装在 Azure 函数中的自定义 Python 代码处理数据。 

此函数是通过[ADF Azure function 活动](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)调用的。 此方法对于轻型数据转换是一个不错的选择。 

* 优点：
    * 在无服务器计算上处理数据，延迟时间相对较低
    * ADF 管道可以调用可实现复杂的数据转换流的[持久 Azure 函数](/azure/azure-functions/durable/durable-functions-overview) 
    * 数据转换的详细信息可被可重复使用并从其他位置调用的 Azure 函数抽象掉
* 缺点：
    * 在使用 ADF 之前，必须创建 Azure Functions
    * Azure Functions 仅适用于短时间运行的数据处理

## <a name="adf-with-custom-component-activity"></a>带有自定义组件活动的 ADF

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

在此选项中，将使用包装到可执行文件中的自定义 Python 代码处理数据。 它使用[ADF 自定义组件活动](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)调用。 此方法更适合用于比上一项技术更大的数据。

* 优点：
    * 数据在[Azure Batch](https://docs.microsoft.com/azure/batch/batch-technical-overview)池上进行处理，后者提供大规模并行和高性能计算
    * 可用于运行繁重的算法并处理大量数据
* 缺点：
    * 在使用 ADF 之前，必须创建 Azure Batch 池
    * 与将 Python 代码包装到可执行文件相关的工程。 处理依赖项和输入/输出参数的复杂性

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF Azure Databricks Python 笔记本

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/)是 Microsoft 云中基于 Apache Spark 的分析平台。

在此方法中，数据转换由[Python 笔记本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)执行，Azure Databricks 群集上运行。 这可能是利用 Azure Databricks 服务的全部功能的最常见方法。 它旨在大规模地进行分布式数据处理。

* 优点：
    * 数据将转换为由 Apache Spark 环境备份的功能最强大的数据处理 Azure 服务
    * 对 Python 和数据科学框架和库（包括 TensorFlow、PyTorch 和 scikit-learn）的本机支持
    * 不需要将 Python 代码包装到函数或可执行模块中。 代码按原样工作。
* 缺点：
    * 在使用 ADF 之前，必须创建 Azure Databricks 基础结构
    * 可能会消耗大量资源，具体取决于 Azure Databricks 的配置
    * 从 "冷" 模式旋转计算群集需要一段时间，使解决方案的延迟较高 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>在 Azure 机器学习管道中使用数据

![aml-数据集](media/how-to-data-ingest-adf/aml-dataset.png)

从 ADF 管道转换的数据保存到数据存储（例如 Azure Blob）。 Azure 机器学习可以使用[数据存储](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)和[数据集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)访问此数据。

每次运行 ADF 管道时，数据将保存到存储中的不同位置。 若要将位置传递到 Azure 机器学习，ADF 管道将调用 Azure 机器学习管道。 调用 ML 管道时，数据位置和运行 ID 作为参数发送。 然后，ML 管道可以使用数据位置创建数据存储/数据集。 

> [!TIP]
> 数据集[支持版本控制](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)，因此 ML 管道可以注册一个新版本的数据集，该数据集指向 ADF 管道中的最新数据。

数据可通过数据存储或数据集访问后，可以使用它来训练 ML 模型。 训练过程可能是从 ADF 中调用的同一 ML 管道的一部分。 或者，它可能是一个单独的进程，如 Jupyter 笔记本中的试验。

由于数据集支持版本控制，每次从管道运行时都会创建一个新版本，因此很容易理解哪个版本的数据用于训练模型。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据工厂中运行 Databricks 笔记本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Azure 机器学习中的数据集定型模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [数据引入管道的 DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

