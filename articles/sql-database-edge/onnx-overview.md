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
ms.date: 11/04/2019
ms.openlocfilehash: 4771db21a0ea5e841dbe12c8ce915b3833a8a30d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692325"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>在 SQL 数据库边缘预览中通过 ONNX 进行机器学习和 AI

Azure SQL 数据库边缘预览版中的机器学习支持[开放式神经网络交换（ONNX）](https://onnx.ai/)格式的模型。 ONNX 是一种开放格式，可用于在各种[机器学习框架和工具](https://onnx.ai/supported-tools)之间交换模型。

## <a name="supported-tool-kits"></a>支持的工具包

ONNXMLTools 使你能够将不同机器学习工具工具包中的模型转换为 ONNX 模型。 目前，对于数值数据类型和单列输入，支持以下工具包：

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML （实验）](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>获取 ONNX 模型

可以通过多种方式获取 ONNX 格式的模型：

- [ONNX 模型 Zoo](https://github.com/onnx/models)：包含用于不同类型任务的多个预先训练的 ONNX 模型。 您可以下载和使用 Windows 机器学习支持的版本。

- [机器学习定型框架的本机导出](https://onnx.ai/supported-tools)：多个定型框架支持对 ONNX 的本机导出功能，这允许您将定型模型保存到 ONNX 格式的特定版本。 例如，Chainer、Caffee2 和 PyTorch。 此外， [Azure 机器学习服务](https://azure.microsoft.com/services/machine-learning-service/)和[Azure 自定义视觉](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)等服务还提供本机 ONNX 导出。

  - 若要了解如何使用自定义视觉在云中训练和导出 ONNX 模型，请参阅[教程：通过 WINDOWS ML 使用自定义视觉使用 ONNX 模型（预览版）](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml)。

- [使用 WinMLTools 转换现有模型](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools)：此 Python 包允许将模型从几个定型框架格式转换为 ONNX。 可以指定要将模型转换为哪个版本的 ONNX，具体取决于应用程序面向的 Windows 版本。 如果你不熟悉 Python，则可以使用[基于 Windows 机器学习 UI 的仪表板](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard)来转换模型。

> [!IMPORTANT]
> 并非所有 ONNX 版本都受 Azure SQL 数据库边缘支持。 目前仅支持通过 ONNX 模型预测数值数据类型。

有了 ONNX 模型之后，可以在 Azure SQL 数据库边缘部署该模型。 然后，可以将[本机计分与预测 t-sql 函数结合](/sql/advanced-analytics/sql-native-scoring/)使用。

## <a name="limitations"></a>限制

目前，此支持仅限于具有**数值数据类型**的模型：

- [int 和 bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real 和 float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
其他数值类型可以通过使用 CAST 和 CONVERT [cast 和 convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)转换为支持的类型。

应将模型输入结构化，以便模型的每个输入对应于表中的单个列。 例如，如果您使用 pandas 数据帧来定型模型，则每个输入应为该模型的一个单独的列。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户部署 SQL 数据库边缘](deploy-portal.md)
- [在 Azure SQL 数据库边缘预览版上部署 ONNX 模型](deploy-onnx.md)
