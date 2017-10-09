---
title: "数据科学虚拟机的示例和演练 - Azure | Microsoft 文档"
description: "数据科学虚拟机的示例和演练"
keywords: "数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 89bb5d255db9ab266d04169a3101e2b694236029
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---


# <a name="samples-on-the-data-science-virtual-machines-dsvm"></a>数据科学虚拟机 (DSVM) 的示例

DSVM 附带了以 Jupyter 笔记本形式完整编制的示例，其中一些示例并非基于 Jupyter。 你可以通过单击桌面或应用程序菜单上的 `Jupyter` 图标访问 Jupyter。  
> [!NOTE]
> 请参阅[访问 Jupyter ](#access-jupyter)部分，以在 DSVM 上启用 Jupyter 笔记本。

## <a name="quick-reference-of-samples"></a>快速参考示例
| 示例类别 | 说明 | 位置 |
| ------------- | ------------- | ------------- |
| R 语言  | 以 R  语言编写的示例，解释了与 Azure 云数据存储连接，比较 Microsoft R Server 或 SQL Server 上的开源 R 和 Microsoft R 以及操作模型等情况。 <br/> [屏幕快照](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python 语言  | 以 Python 语言编写的示例，解释了与 Azure 云数据存储连接并使用 Azure机器学习等情况。  <br/> [屏幕快照](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia 语言  | 以 Julia 语言编写的示例，详细介绍了 Julia 中的绘图，Julia 中的深度学习，以及从 Julia 调用 C 和 Python 等。 <br/> [屏幕快照](#julia-language) |<br/> Windows：<br/> `~notebooks/Julia_notebooks`<br/><br/>`~notebooks`<br/><br/> Linux：<br/> `~notebooks/julia`<br/><br/> |
| CNTK <br/> (Microsoft Cognitive Toolkit)  | 深度学习示例由微软的 Cognitive Toolkit 团队发布。  <br/> [屏幕快照](#cntk) | <br/>Windows：<br/> `~notebooks/CNTK/Tutorials`<br/><br/>`~/samples/CNTK-Samples-2-0/Examples`<br/><br/> Linux：<br/> `~notebooks/CNTK`<br/> <br/>|
| MXnet Notebook  | 深度学习示例利用基于 MXnet 的神经网络。 有多种从初级到高级场景的笔记本。  <br/> [屏幕快照](#mxnet) | <br/>`~notebooks/mxnet`<br/> <br/>|
| Azure 机器学习 AzureML  | 与 Azure 机器学习工作室进行交互，并从本地培训的模型中创建 Web Service 终结点，用于基于云的评分工作流。 <br/> [屏幕快照](#azureml) | <br/>`~notebooks/azureml`<br/> <br/>|
| **caffe2** | 深度学习示例利用基于 caffe2 的神经网络。 有多个旨在让用户熟悉 caffe2 以及如何有效使用它的笔记本，包括图像预处理、数据集创建、回归和使用预先训练的模型等示例。 <br/> [屏幕快照](#caffe2) | <br/>`~notebooks/caffe2`<br/><br/> |
| **H2O**   | 基于 Python 的示例利用 H2O 处理许多现实世界的场景问题。 <br/> [屏幕快照](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML 语言  | 通过 Apache Spark 2.0 上的 pySpark 2.0 利用 Spark MLlib 工具包的功能和特性的示例  <br/> [屏幕快照](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/><br/> |
| MMLSpark 语言  | 利用 MMLSpark（Apache Spark 适用的 Microsoft 机器学习）的各种示例，是为 Apache Spark 提供大量深度学习和数据科学工具的框架。 <br/> [屏幕快照](#sparkml) | <br/>`~notebooks/MMLSpark`<br/><br/> |
| TensorFlow  | 使用 TensorFlow 框架实现的多个不同神经网络示例和技术。 <br/> [屏幕快照](#tensorflow) | <br/>`~notebooks/tensorflow`<br/><br/> |
| XGBoost | XGBoost 中的标准机器学习示例用于分类、回归等场景。 <br/> [屏幕快照](#xgboost) | <br/>`~samples/xgboost/demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>访问 Jupyter 

通过转到 Windows 上的 **`https://localhost:9999`** 或 Ubuntu 上的 **`https://localhost:8000`** 来访问 Jupyter 主页。


### <a name="enabling-jupyter-access-from-browser"></a>从浏览器启用 Jupyter 访问权限

Windows DSVM

从桌面快捷方式运行 **`Jupyter SetPassword`**，并按照提示设置/重置 Jupyter 密码并启动 Jupyter 进程。 
<br/>![启用 Jupyter 异常](./media/jupyter-setpassword.png)<br/>
通过在浏览器上访问 **`https://localhost:9999`**，在虚拟机上成功启动 Jupyter 进程后，你才能访问 Jupyter 主页。 请参阅屏幕快照以添加异常，并通过浏览器启用 Jupyter 访问
<br/>![启用 Jupyter 异常](./media/windows-jupyter-exception.png)<br/>
使用你刚设置的新密码登录。
<br/>
**Linux DSVM**

你可以通过浏览器上的 **`https://localhost:8000`** 来访问虚拟机上的 Jupyter 主页。 请参阅屏幕快照以添加异常，并通过浏览器启用 Jupyter 访问。
<br/>![启用 Jupyter 异常](./media/ubuntu-jupyter-exception.png)<br/>
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

## <a name="cntk"></a>CNTK 
<br/>![CNTK 示例](./media/cntk-samples2.png)<br/>
<br/>![CNTK 示例](./media/cntk-samples.png)<br/>

## <a name="mxnet"></a>MXNet
<br/>![MXnet 示例](./media/mxnet-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![AzurekML 示例](./media/azureml-samples.png)<br/>

## <a name="caffe2"></a>Caffe2 
<br/>![caffe2 示例](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O 示例](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML 示例](./media/sparkml-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow 示例](./media/tensorflow-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost 示例](./media/xgboost-samples.png)<br/>


