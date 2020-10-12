---
title: 示例程序及机器学习演练
titleSuffix: Azure Data Science Virtual Machine
description: 通过这些示例和演练，了解如何使用 Data Science Virtual Machine 处理常见任务和方案。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 98a1cd883867aa2f785b15f4c7ae4f2266a421ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87012563"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure Data Science Virtual Machine 示例

Azure Data Science Virtual Machine (DSVM) 包含一整套示例代码。 这些示例包括用 Python 和 R 等语言写成的 Jupyter 笔记本和脚本。
> [!NOTE]
> 若要详细了解如何在 Data Science Virtual Machine 上运行 Jupyter 笔记本，请参阅[访问 Jupyter](#access-jupyter) 部分。

## <a name="prerequisites"></a>先决条件

为了运行这些示例，必须预配 [Ubuntu Data Science Virtual Machine](./dsvm-ubuntu-intro.md)。

## <a name="available-samples"></a>可用示例
| 示例类别 | 说明 | 位置 |
| ------------- | ------------- | ------------- |
| R 语言  | 示例介绍了多种方案，例如如何连接到基于 Azure 的云数据存储，以及如何比较开源 R 和 Microsoft Machine Learning Server。 此外还介绍了如何在 Microsoft Machine Learning Server 和 SQL Server 上操作模型。 <br/> [R 语言](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 语言  | 示例介绍了多种方案，例如如何连接到基于 Azure 的云数据存储，以及如何使用 Azure 机器学习。  <br/> [Python 语言](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 语言  | 详细说明了 Julia 中的绘图和深度学习。 此外还介绍了如何通过 Julia 调用 C 和 Python。 <br/> [Julia 语言](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure 机器学习  | 介绍了如何使用机器学习生成机器学习和深度学习模型。 在任意位置部署模型。 使用自动化机器学习和智能超参数优化。 还使用模型管理和分布式定型。 <br/> [机器学习](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Notebook  | 使用基于 PyTorch 的神经网络的深度学习示例。 Notebook 从新手方案到高级方案都有涵盖。  <br/> [PyTorch Notebook](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  使用 TensorFlow 框架实现的各种神经网络示例和技术。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft 认知工具包 <br/>   | 由 Microsoft 的 Cognitive Toolkit 团队发布的深度学习示例。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | 使用基于 Caffe2 的神经网络的深度学习示例。 用户可通过多个笔记本熟悉 Caffe2 以及如何有效使用它。 示例不仅包括图像预处理和数据集创建， 还包括回归以及如何使用预定型模型。 <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 基于 Python 的示例，使用 H2O 处理实际问题方案。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 语言  | 通过 pySpark 和 MMLSpark 使用 Apache Spark MLLib 工具箱功能的示例：面向 Apache Spark 2.x 上的 Apache Spark 的 Microsoft 机器学习。  <br/> [SparkML 语言](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | XGBoost 中的标准机器学习示例，用于分类、回归等场景。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>访问 Jupyter 

若要访问 Jupyter，请选择桌面或应用程序菜单中的 Jupyter 图标****。 还可以在 Linux 版的 DSVM 上访问 Jupyter。 若要在 Web 浏览器中执行远程访问，请在 Ubuntu 上转到 `https://<Full Domain Name or IP Address of the DSVM>:8000`。

若要添加例外并允许通过浏览器访问 Jupyter，请使用以下指南：


![启用 Jupyter 异常](./media/ubuntu-jupyter-exception.png)


使用登录 Data Science Virtual Machine 所用的密码进行登录。
<br/>

**Jupyter 主页**
<br/>![Jupyter 主页](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R 语言 
<br/>![R 示例](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python 语言
<br/>![Python 示例](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia 语言 
<br/>![Julia 示例](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure 机器学习 
<br/>![Azure 机器学习示例](./media/azureml-samples.png)<br/>

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

