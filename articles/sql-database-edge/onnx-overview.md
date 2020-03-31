---
title: 在 Azure SQL 数据库边缘预览中，使用 ONNX 进行机器学习和 AI |微软文档
description: Azure SQL 数据库边缘预览中的机器学习支持开放神经网络交换 （ONNX） 格式的模型。 ONNX 是一种开放格式，可用于在各种机器学习框架和工具之间交换模型。
keywords: 部署 sql 数据库边缘
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366265"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>在 SQL 数据库边缘预览中，使用 ONNX 进行机器学习和 AI

Azure SQL 数据库边缘预览中的机器学习支持[开放神经网络交换 （ONNX）](https://onnx.ai/)格式的模型。 ONNX 是一种开放格式，可用于在各种[机器学习框架和工具](https://onnx.ai/supported-tools)之间交换模型。

## <a name="overview"></a>概述

要推断 Azure SQL 数据库边缘中的机器学习模型，首先需要获取模型。 这可以是经过预先训练的模型，也可以是使用您选择的框架训练的自定义模型。 Azure SQL 数据库边缘支持 ONNX 格式，您需要将模型转换为此格式。 不应对模型准确性产生影响，一旦您有了 ONNX 模型，就可以在 Azure SQL 数据库边缘部署模型，并使用[预测 T-SQL 函数使用本机评分](/sql/advanced-analytics/sql-native-scoring/)。

## <a name="get-onnx-models"></a>获取 ONNX 模型

要获取 ONNX 格式的模型，：

- **模型构建服务**：Azure 机器学习和[Azure 自定义视觉服务](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)[中的自动机器学习功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)等服务支持以 ONNX 格式直接导出已训练的模型。

- [**转换和/或导出现有模型**](https://github.com/onnx/tutorials#converting-to-onnx-format)：多个培训框架（例如[PyTorch、](https://pytorch.org/docs/stable/onnx.html)链条和 Caffe2）支持本机导出功能到 ONNX，这允许您将训练的模型保存到 ONNX 格式的特定版本。 对于不支持本机导出的框架，有独立的 ONNX 转换器可安装包，使您能够将从不同机器学习框架训练的模型转换为 ONNX 格式。

     **支持的框架**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [张力流](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [学习](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    有关支持的框架和示例的完整列表，请参阅转换为[ONNX 格式](https://github.com/onnx/tutorials#converting-to-onnx-format)。

## <a name="limitations"></a>限制

目前，并非所有 ONNX 模型都受 Azure SQL 数据库边缘的支持。 支持仅限于具有**数字数据类型的**模型：

- [int 和 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [真实和浮动](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
其他数值类型可以使用[CAST 和 CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)转换为支持的类型。

模型输入的结构应使模型的每个输入对应于表中的单个列。 例如，如果使用熊猫数据框来训练模型，则每个输入应该是模型的单独列。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)
- [在 Azure SQL 数据库边缘预览版上部署 ONNX 模型](deploy-onnx.md)
