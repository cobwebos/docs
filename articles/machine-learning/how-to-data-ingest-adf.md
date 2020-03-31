---
title: 使用 Azure 数据工厂引入数据
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274783"
---
# <a name="data-ingestion-with-azure-data-factory"></a>使用 Azure 数据工厂引入数据

在本文中，您将了解如何使用 Azure 数据工厂 （ADF） 构建数据引入管道。 此管道用于引入数据以与 Azure 机器学习一起使用。 Azure 数据工厂允许您轻松提取、转换和加载 （ETL） 数据。 数据转换并加载到存储中后，可用于训练机器学习模型。

简单的数据转换可以通过本机 ADF 活动和[数据流](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity)等工具进行处理。 当涉及到更复杂的方案时，可以使用一些自定义代码处理数据。 例如，Python 或 R 代码。

有几个常用技术使用 Azure 数据工厂在引入期间转换数据。 每种技术都有优缺点，可以确定它是否适合特定的用例：

| 方法 | 优点 | 缺点 |
| ----- | ----- | ----- |
| ADF + Azure 功能 | 低延迟、无服务器计算</br>有状态函数</br>可重用函数 | 仅适用于短运行处理 |
| ADF + 自定义组件 | 大规模并行计算</br>适合重型算法 | 将代码包装到可执行文件中</br>处理依赖项和 IO 的复杂性 |
| ADF = Azure 数据砖笔记本 | Apache Spark</br>本机 Python 环境 | 可能很贵</br>创建群集最初需要时间并增加延迟

## <a name="adf-with-azure-functions"></a>具有 Azure 功能的 ADF

![adf 功能](media/how-to-data-ingest-adf/adf-function.png)

Azure 函数允许您运行小块代码（函数），而不必担心应用程序基础结构。 在此选项中，数据使用自定义 Python 代码打包到 Azure 函数中处理。 

函数使用[ADF Azure 函数活动](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity)调用。 此方法是轻量级数据转换的一个好选项。 

* 优点：
    * 数据在具有相对较低延迟的无服务器计算上进行处理
    * ADF 管道可以调用[持久 Azure 函数](/azure/azure-functions/durable/durable-functions-overview)，该函数可以实现复杂的数据转换流 
    * 数据转换的详细信息由 Azure 函数抽象为，该函数可以从其他地方重用和调用
* 缺点：
    * 在与 ADF 使用之前，必须创建 Azure 函数
    * Azure 函数仅适用于短期运行的数据处理

## <a name="adf-with-custom-component-activity"></a>具有自定义组件活动的 ADF

![adf-自定义组件](media/how-to-data-ingest-adf/adf-customcomponent.png)

在此选项中，数据使用自定义 Python 代码打包到可执行文件中进行处理。 它使用[ADF 自定义组件活动](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)调用。 与以前的技术相比，此方法更适合大型数据。

* 优点：
    * 数据在[Azure 批处理](https://docs.microsoft.com/azure/batch/batch-technical-overview)池上处理，该池提供大规模并行和高性能计算
    * 可用于运行大量算法和处理大量数据
* 缺点：
    * 在与 ADF 使用之前，必须创建 Azure 批处理池
    * 与将 Python 代码包装到可执行文件相关的工程。 处理依赖项和输入/输出参数的复杂性

## <a name="adf-with-azure-databricks-python-notebook"></a>带有 Azure 数据砖 Python 笔记本的 ADF

![adf-数据砖](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure 数据砖块](https://azure.microsoft.com/services/databricks/)是 Microsoft 云中基于 Apache Spark 的分析平台。

在此技术中，数据转换由在 Azure 数据砖块群集上运行的[Python 笔记本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)执行。 这可能是利用 Azure 数据砖块服务的全部功能的最常见方法。 它专为大规模分布式数据处理而设计。

* 优点：
    * 数据在最强大的数据处理 Azure 服务上转换，该服务由 Apache Spark 环境备份
    * Python 的本机支持以及数据科学框架和库，包括 TensorFlow、PyTorch 和 scikit 学习
    * 无需将 Python 代码包装到函数或可执行模块中。 代码的工作方式相同。
* 缺点：
    * 在与 ADF 使用之前，必须创建 Azure 数据砖块基础结构
    * 根据 Azure 数据砖块配置，可能非常昂贵
    * 从"冷"模式旋转计算群集需要一些时间，为解决方案带来高延迟 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>在 Azure 机器学习管道中使用数据

![aml 数据集](media/how-to-data-ingest-adf/aml-dataset.png)

ADF 管道中转换后的数据将保存到数据存储（如 Azure Blob）。 Azure 机器学习可以使用[数据存储](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)和[数据集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)访问此数据。

每次 ADF 管道运行时，数据都会保存到存储中的不同位置。 要将位置传递给 Azure 机器学习，ADF 管道将调用 Azure 机器学习管道。 调用 ML 管道时，数据位置和运行 ID 将作为参数发送。 然后，ML 管道可以使用数据位置创建数据存储/数据集。 

> [!TIP]
> 数据集[支持版本控制](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)，因此 ML 管道可以注册指向 ADF 管道的最新数据的新版本数据集。

通过数据存储或数据集访问数据后，可以使用它训练 ML 模型。 训练过程可能是从 ADF 调用的同一 ML 管道的一部分。 或者，它可能是一个单独的过程，如在Jupyter笔记本中的实验。

由于数据集支持版本控制，并且每个从管道运行都会创建一个新版本，因此很容易理解数据的哪个版本用于训练模型。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据工厂中运行数据砖块笔记本](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [在 Azure 机器学习中使用数据集训练模型](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [数据引入管道的 DevOps](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

