---
title: 在 Azure SQL 数据库边缘预览中通过 ONNX 进行机器学习和 AI |Microsoft Docs
description: Azure SQL 数据库边缘预览版中的机器学习支持开放式神经网络交换（ONNX）格式的模型。 ONNX 是一种开放格式，可用于在各种机器学习框架和工具之间交换模型。
keywords: 部署 sql 数据库边缘
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822850"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>在 SQL 数据库边缘预览中通过 ONNX 进行机器学习和 AI

Azure SQL 数据库边缘预览版中的机器学习支持[开放式神经网络交换（ONNX）](https://onnx.ai/)格式的模型。 ONNX 是一种开放格式，可用于在各种[机器学习框架和工具](https://onnx.ai/supported-tools)之间交换模型。

## <a name="overview"></a>概述

若要在 Azure SQL 数据库边缘推断机器学习模型，首先需要获取模型。 这可以是预先训练的模型，也可以是使用你选择的框架训练的自定义模型。 Azure SQL 数据库边缘支持 ONNX 格式，需要将模型转换为此格式。 应该不会对模型准确性产生任何影响，一旦您具有 ONNX 模型，就可以在 Azure SQL 数据库边缘中部署该模型，并对[预测 t-sql 函数使用本机计分](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="get-onnx-models"></a>获取 ONNX 模型

可以通过多种方式获取 ONNX 格式的模型：

- [ONNX 模型 Zoo](https://github.com/onnx/models)：包含多个预先训练的 ONNX 模型，适用于可下载并可供使用的不同类型的任务。

- [ML 培训框架的本机导出](https://onnx.ai/supported-tools)：多个定型框架支持 ONNX 的本机导出功能，这使你可以将定型模型保存到 ONNX 格式的特定版本，包括[PyTorch](https://pytorch.org/docs/stable/onnx.html)、Chainer 和 Caffe2。 此外，模型生成服务（例如 Azure 机器学习和[Azure 自定义影像服务](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)[中的自动机器学习功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)）提供 ONNX 导出。

- [转换现有模型](https://github.com/onnx/tutorials#converting-to-onnx-format)：对于不支持本机导出的框架，有将模型转换为 ONNX 格式的单独包。 有关示例和教程，请参阅[转换为 ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。 

### <a name="supported-frameworks"></a>支持的框架

ONNX 转换器使你能够将来自不同机器学习框架的定型模型转换为 ONNX 格式。 常用转换器包括： 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

有关受支持框架的完整列表，请参阅[转换为 ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。

## <a name="limitations"></a>限制

目前，Azure SQL 数据库边缘并不支持所有 ONNX 模型。 此支持仅限于具有**数值数据类型**的模型：

- [int 和 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real 和 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
可以使用[CAST 和 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)将其他数值类型转换为支持的类型。

应将模型输入结构化，以便模型的每个输入对应于表中的单个列。 例如，如果您使用 pandas 数据帧来定型模型，则每个输入应为该模型的一个单独的列。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)
- [在 Azure SQL 数据库边缘预览版上部署 ONNX 模型](deploy-onnx.md)
