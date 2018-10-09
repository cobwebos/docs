---
title: 数据科学虚拟机的示例和演练 - Azure | Microsoft 文档
description: 数据科学虚拟机的示例和演练
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: a1f15b805d2f27152d9ba85608ce0dc1d1aac21e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392551"
---
# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>数据科学虚拟机 (DSVM) 的示例

DSVM 包括一组综合的示例代码，代码的形式既有 Jupyter Notebook，又有使用 Python 和 R 等语言编写的脚本。    
> [!NOTE]
> 有关在 DSVM 上运行 Jupyter Notebook 的详细信息，请参阅[访问 Jupyter](#access-jupyter) 部分。

## <a name="quick-reference-of-samples"></a>快速参考示例
| 示例类别 | Description | 位置 |
| ------------- | ------------- | ------------- |
| R 语言  | 以 R  语言编写的示例，解释了与 Azure 云数据存储连接，比较 Microsoft R Server 或 SQL Server 上的开源 R 和 Microsoft R 以及操作模型等情况。 <br/> [屏幕快照](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 语言  | 以 Python 语言编写的示例，解释了与 Azure 云数据存储连接并使用 Azure机器学习等情况。  <br/> [屏幕快照](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 语言  | 以 Julia 语言编写的示例，详细介绍了 Julia 中的绘图，Julia 中的深度学习，以及从 Julia 调用 C 和 Python 等。 <br/> [屏幕快照](#julia-language) |<br/> Windows：<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| Azure 机器学习 AzureML  | 使用 **Azure 机器学习**服务构建 ML 和深度学习模型，并在任何地方部署模型。 利用自动化 ML、智能超参数优化、模型管理、分布式训练等功能。 <br/> [屏幕快照](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| **PyTorch** Notebook  | 利用基于 PyTorch 的神经网络的深度学习示例。 有多种从初级到高级场景的笔记本。  <br/> [屏幕快照](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  | 使用 TensorFlow 框架实现的多个不同神经网络示例和技术。 <br/> [屏幕快照](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| CNTK <br/> (Microsoft Cognitive Toolkit)  | 深度学习示例由微软的 Cognitive Toolkit 团队发布。  <br/> [屏幕快照](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| **caffe2** | 深度学习示例利用基于 caffe2 的神经网络。 有多个旨在让用户熟悉 caffe2 以及如何有效使用它的笔记本，包括图像预处理、数据集创建、回归和使用预先训练的模型等示例。 <br/> [屏幕快照](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| **H2O**   | 基于 Python 的示例利用 H2O 处理许多现实世界的场景问题。 <br/> [屏幕快照](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 语言  | 在 **Apache Spark 2.x** 上通过 **pySpark** 和 **MMLSpark - 用于 Apache Spark 的 Microsoft 机器学习**利用 Spark 的 **MLlib** 工具包的特性和功能的示例。  <br/> [屏幕快照](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | XGBoost 中的标准机器学习示例用于分类、回归等场景。 <br/> [屏幕快照](#xgboost) | <br/>Windows：<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>访问 Jupyter 

你可以通过单击桌面或应用程序菜单上的 `Jupyter` 图标访问 Jupyter。 还可以通过访问 Ubuntu 上的 **`https://<Full Domain Name or IP Address of the DSVM>:8000`**，从 Web 浏览器远程访问 DSVM 的 Linux 版本上的 Jupyter。

请参阅屏幕快照以添加异常，并通过浏览器启用 Jupyter 访问。


![启用 Jupyter 异常](./media/ubuntu-jupyter-exception.png)


使用与登录 DSVM 相同的密码登录。
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

