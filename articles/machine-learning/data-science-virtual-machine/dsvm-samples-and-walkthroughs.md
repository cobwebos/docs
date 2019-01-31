---
title: Data Science Virtual Machine 示例和演练 - Azure | Microsoft Docs
description: 了解介绍如何使用 Data Science Virtual Machine 完成常见任务和方案的示例和演练。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475748"
---
# <a name="samples-on-data-science-virtual-machines"></a>Data Science Virtual Machine 示例

Azure Data Science Virtual Machine 包含一整套示例代码。 示例代码的形式为，用 Python 和 R 等语言编写的 Jupyter Notebook 和脚本。 
> [!NOTE]
> 若要详细了解如何在 Data Science Virtual Machine 上运行 Jupyter Notebook，请参阅[访问 Jupyter](#access-jupyter) 部分。

## <a name="quick-reference-of-samples"></a>示例快速参考
| 示例类别 | 说明 | 位置 |
| ------------- | ------------- | ------------- |
| R 语言  | 用 R 语言编写的示例，展示了如何与 Azure 云数据存储连接等方案。 此外，还展示了如何比较开放源代码 R 和 Microsoft R，以及如何在 Microsoft R Server 或 SQL Server 上实现模型。 <br/> [R 语言](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 语言  | 用 Python 语言编写的示例，展示了如何与 Azure 云数据存储连接并使用 Azure 机器学习等方案。  <br/> [Python 语言](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 语言  | 用 Julia 语言编写的示例，详细展示了如何使用 Julia 语言进行绘图和深度学习。 此外，还展示了如何通过 Julia 调用 C 和 Python。 <br/> [Julia 语言](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure 机器学习  | 使用机器学习生成机器学习和深度学习模型。 在任意位置部署模型。 使用自动化机器学习和智能超参数优化。 还使用模型管理和分布式定型。 <br/> [机器学习](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Notebook  | 使用基于 PyTorch 的神经网络的深度学习示例。 Notebook 从新手方案到高级方案都有涵盖。  <br/> [PyTorch Notebook](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  使用 TensorFlow 框架实现的不同神经网络示例和技术。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft 认知工具包 <br/>   | 深度学习示例由微软的 Cognitive Toolkit 团队发布。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | 使用基于 caffe2 的神经网络的深度学习示例。 用户可通过多个 Notebook 熟悉 caffe2 以及如何有效使用它。 示例不仅包括图像预处理和数据集创建， 还包括回归以及如何使用预定型模型。 <br/> [caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 基于 Python 的示例，使用 H2O 处理实际方案问题。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 语言  | 通过 pySpark 和 MMLSpark（Apache Spark 2.x 上适用于 Apache Spark 的 Microsoft 机器学习）使用 Spark MLlib 工具包功能的示例。  <br/> [SparkML 语言](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | XGBoost 中的标准机器学习示例，用于分类和回归等方案。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>访问 Jupyter 

若要访问 Jupyter，请选择桌面或应用程序菜单中的 `Jupyter` 图标。 还可以在 Linux 版本的 Data Science Virtual Machine 上访问 Jupyter。 若要在 Web 浏览器中执行远程访问，可以在 Ubuntu 上访问 `https://<Full Domain Name or IP Address of the DSVM>:8000`。

若要添加异常并让 Jupyter 可通过浏览器访问，请参阅下面的屏幕截图。


![启用 Jupyter 异常](./media/ubuntu-jupyter-exception.png)


使用登录 Data Science Virtual Machine 所用的相同密码进行登录。
<br/>

**Jupyter 主页**
<br/>![Jupyter 主页](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 语言 
<br/>![R 示例](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 语言
<br/>![Python 示例](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 语言 
<br/>![Julia 示例](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML 示例](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch 示例](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 示例](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK 示例](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 示例](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 示例](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 示例](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 示例](./media/xgboost-samples.png)<br/>

