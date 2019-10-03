---
title: 示例 & 演练
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
ms.openlocfilehash: ad58adb85077a27bce65595738ffdbd92bace9bd
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802455"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Azure 数据科学虚拟机上的示例

Azure 数据科学虚拟机（Dsvm）包括一组全面的示例代码。 这些示例包括 Python 和 R 等语言的 Jupyter 笔记本和脚本。
> [!NOTE]
> 有关如何在数据科学虚拟机上运行 Jupyter 笔记本的详细信息，请参阅[Access Jupyter](#access-jupyter)部分。

## <a name="prerequisites"></a>先决条件

若要运行这些示例，必须已设置 Data Science Virtual Machine。 请参阅适用于[Windows](./provision-vm.md)和[Ubuntu](./dsvm-ubuntu-intro.md)的快速入门。

## <a name="available-samples"></a>可用示例
| 示例类别 | 描述 | 位置 |
| ------------- | ------------- | ------------- |
| R 语言  | 示例说明了如何连接到基于 Azure 的云数据存储的方案，以及如何比较开源 R 和 Microsoft Machine Learning Server。 它们还介绍了如何在 Microsoft Machine Learning Server 和 SQL Server 上操作模型。 <br/> [R 语言](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 语言  | 示例介绍了如何连接到基于 Azure 的云数据存储以及如何使用 Azure 机器学习的方案。  <br/> [Python 语言](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 语言  | 提供 Julia 中的绘图和深度学习的详细说明。 还介绍了如何从 Julia 调用 C 和 Python。 <br/> [Julia 语言](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure 机器学习  | 说明如何通过机器学习来构建机器学习和深度学习模型。 在任意位置部署模型。 使用自动化机器学习和智能超参数优化。 还使用模型管理和分布式定型。 <br/> [机器学习](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch Notebook  | 使用基于 PyTorch 的神经网络的深度学习示例。 Notebook 从新手方案到高级方案都有涵盖。  <br/> [PyTorch Notebook](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  使用 TensorFlow 框架实现的各种神经网络示例和技术。 <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft 认知工具包 <br/>   | Microsoft Cognitive Toolkit 团队发布的深度学习示例。  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | 使用基于 Caffe2 的神经网络的深度学习示例。 几个笔记本使用户熟悉 Caffe2 以及如何有效地使用它。 示例包括图像预处理和数据集创建。 它们还包括回归以及如何使用预先训练模型。 <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | 针对实际问题方案使用 H2O 的基于 Python 的示例。 <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 语言  | 使用 Apache Spark MLLib 工具包通过 pySpark 和 MMLSpark 功能的示例：用于 Apache Spark 2.x 上的 Apache Spark 的 Microsoft 机器学习。  <br/> [SparkML 语言](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | XGBoost 中的标准机器学习示例，用于分类和回归等方案。 <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>访问 Jupyter 

若要访问 Jupyter，请选择 "桌面" 或 "应用程序" 菜单上的 " **Jupyter** " 图标。 你还可以在 DSVM 的 Linux 版本上访问 Jupyter。 若要从 web 浏览器远程访问，请在 Ubuntu 上访问 `https://<Full Domain Name or IP Address of the DSVM>:8000`。

若要添加例外并通过浏览器提供 Jupyter 访问，请使用以下指南：


![启用 Jupyter 异常](./media/ubuntu-jupyter-exception.png)


使用登录到 Data Science Virtual Machine 时所用的同一密码进行登录。
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

