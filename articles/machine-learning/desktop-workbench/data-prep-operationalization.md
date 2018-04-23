---
title: 有关如何使用 Azure 机器学习数据准备操作化的深入指南 | Microsoft Docs
description: 本文档提供了有关执行以前设计的数据源和数据准备包的详细信息
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="data-preparation-operationalization"></a>数据准备操作化 

## <a name="operationalization-scenario"></a>操作化方案

下面是用户可能遇到的不同的数据准备操作化方案。

### <a name="develop-your-model-based-on-training-data"></a>根据定型数据来开发模型

假定你要创建并部署一项实时评分 API 服务。 第一步是开发一个模型，根据定型数据集进行评分。 数据准备会将定型数据的示例作为新的数据源导入。 准备好数据以后，即可执行 DataPrep 包中提供的准备步骤。 用户可以使用 AzureML 试验帐户循环访问机器学习模型，为定型数据中的每个输入生成标签。

需要更新数据的特性时，用户可以返回到 DataPrep 包以新的方式准备数据并保存这些步骤。 这种生成新特性并调整其机器学习模型的迭代过程会一直继续，直至模型能够精确地对测试数据评分。 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>将模型部署到 Azure 模型管理服务

现在，你有了要实时评分的客户数据。 可以使用 Azure 模型管理服务，通过 AzureML Python CLI 将此模型部署为服务。 该部署的服务会公开一个 REST 终结点，用于实时接收客户数据，并从定型的模型返回相应的输出标签。

为了方便使用，此模型终结点接受 JSON 格式的数据。 输入应该是一个表示二维数组的 JSON 字符串，该字符串在经过 ReadJSONLiteral 转换后，会转换成 DataPrep 数据源。 然后，可以针对流式传输的 JSON 数据执行在试验阶段创建的数据准备包。 生成的数据帧然后又可以传递给定型模型进行评分。

## <a name="read-json-literal-transformation"></a>读取 JSON 文本转换

### <a name="description"></a>说明

考虑到操作化，数据准备包含一个 **ReadJsonLiteral** 转换，用于执行活动并生成 Pandas 或 Spark 数据帧。 此转换以独特的方式使用现有数据准备包和 JSON 数据源作为输入。 此转换通过 DataPrep Python CLI 公开。

### <a name="instructions"></a>说明

#### <a name="pythoncli"></a>PythonCLI

在 Azure Machine Learning Workbench 中打开命令行界面（“文件”>“打开命令提示符”）。

在此示例中，请使用 TrainingPreparationSteps 数据准备包来准备数据并为每个输入添加卷功能。

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` 具有以下可选参数
 - `dataflow_idx`：指定需要在包中执行的数据流的索引
 - `secrets`：机密存储字典（键：secretId，值：存储的机密）
 - `spark`：提供一个 spark 会话，用于 spark 执行

#### <a name="input"></a>输入

一个二维数组输入（“数组的数组”）。 在 Python 中，该输入应该是列表的列表。 由于 JSON 没有本机日期类型，因此任何 Python datetime.datetime 对象都会转换为 ISO 格式的日期字符串。 对于 REST 终结点，输入应该是表示二维 JSON 数组的 JSON 文本字符串。

#### <a name="output"></a>输出

Pandas 或 Spark 数据帧。 数据帧的类型取决于在运行配置 (`.runconfig`) 中选择的框架。 生成的数据帧可以作为输入传递给定型模型进行评分。
