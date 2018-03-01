---
title: "深度学习和人工智能框架 - Azure | Microsoft Docs"
description: "深度学习和人工智能框架"
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
ms.openlocfilehash: 89a8cde0dbb7fe7ccfbb6d068411aaf5488c532f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deep-learning-and-ai-frameworks"></a>深度学习和人工智能框架
[数据科学虚拟机](http://aka.ms/dsvm) (DSVM) 和[深度学习 VM](http://aka.ms/dsvm/deeplearning) 支持多个深度学习框架，可帮助生成具有预测分析和认知功能（例如图像和语言理解）的人工智能 (AI) 应用程序。 

下文详细介绍 DSVM 上可用的所有深度学习框架。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft 认知工具包

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | Microsoft 认知工具包 (CNTK) 在 Python 2.7（_root_ 环境下）以及 Python 3.5（_py35_ 环境下）中安装。   |
| 示例链接      | 包含 Jupyter 笔记本示例。     |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | 打开 Jupyter，然后查找 CNTK 文件夹  |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | 在 Linux 上，TensorFlow 在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。 在 Windows 上，Tensorflow 在 Python 3.5 (_py35_) 环境中安装。  |
| 示例链接      | 包含 Jupyter 笔记本示例。     |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | 打开 Jupyter，然后查找 TensorFlow 文件夹。  |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | Keras 在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。   |
| 示例链接      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM 上的相关工具      | Microsoft 认知工具包、TensorFlow、Theano      |
| 如何使用/运行它？    | 从 Github 下载示例，将其复制到 ~/notebooks 下的目录中并在 Jupyter 中打开   |




## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Caffe 在 `/opt/caffe` 中安装。    |
| 示例链接      | `/opt/caffe/examples` 中包含示例。      |
| DSVM 上的相关工具      | Caffe2      |
### <a name="how-to-use--run-it"></a>如何使用/运行它？  

使用 X2Go 登录 VM，然后启动新终端并输入

```
cd /opt/caffe/examples
jupyter notebook
```

系统将打开显示示例笔记本的新浏览器窗口。

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Caffe2 在 `/opt/caffe2` 中安装。 它还可用于 Python 2.7 (_root_) conda 环境。     |
| 示例链接      | 包含 Jupyter 笔记本示例     |
| DSVM 上的相关工具      | Caffe      |
| 如何使用/运行它？    | 打开 Jupyter，然后导航到 Caffe2 目录，查找示例笔记本。 某些笔记本需要在 Python 代码中设置 Caffe2 根；输入 /opt/caffe2。   |


## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | Chainer 在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。 同时还会安装 ChainerRL 和 ChainerCV。   |
| 示例链接      | 包含 Jupyter 笔记本示例。      |
| DSVM 上的相关工具      | Caffe      |

### <a name="how-to-use--run-it"></a>如何使用/运行它？  

在终端上，激活所需的 Python 版本（_root_ 或 _py35_），运行 _python_，然后导入 Chainer。 在 Jupyter 中，选择 Python 2.7 或 3.5 内核，然后导入 Chainer。


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 它是什么？   | H2O 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Deep Water 在 `/dsvm/tools/deep_water` 中安装。   |
| 示例链接      | 通过 Deep Water 服务器提供示例。      |
| DSVM 上的相关工具      | H2O、Sparkling Water      |

### <a name="how-to-use--run-it"></a>如何使用/运行它？  

使用 X2Go 连接到 VM。 在终端上，启动 Deep Water 服务器：

    java -jar /dsvm/tools/deep_water/h2o.jar

然后打开浏览器并连接到 `http://localhost:54321`。



## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | MXNet 在 Windows 上安装在 `C:\dsvm\tools\mxnet` 中，在 Linux 上安装在 `/dsvm/tools/mxnet` 中。 Python 绑定在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。 同时还会安装 R 绑定。   |
| 示例链接      | 包含 Jupyter 笔记本示例。    |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | 打开 Jupyter，然后查找 mxnet 文件夹  |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| 它是什么？   | 来自 NVIDIA 的深度学习系统，可用于快速训练深度学习模型      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | DIGITS 在 `/dsvm/tools/DIGITS` 中安装，可通过名为 _digits_ 的服务获得。   |
### <a name="how-to-use--run-it"></a>如何使用/运行它？  

使用 X2Go 登录 VM。 在终端上，启动服务：

    sudo systemctl start digits

该服务需要约一分钟启动。 打开 Web 浏览器并导航到 `http://localhost:5000`。



## <a name="nvdia-smi"></a>nvdia-smi

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于查询 GPU 活动的 NVIDIA 工具      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | _nvidia-smi_ 在系统路径上可用。   |
| 如何使用/运行它？ | 启动命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 _nvidia-smi_。



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Theano 在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。   |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | 在终端上，激活所需的 Python 版本（root 或 py35），运行 python，然后导入 theano。 在 Jupyter 中，选择 Python 2.7 或 3.5 内核，然后导入 theano。  |



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Torch 在 `/dsvm/tools/torch` 中安装。 PyTorch 在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。   |
| 示例链接      | Torch 示例位于 `/dsvm/samples/torch`。 PyTorch 示例位于 `/dsvm/samples/pytorch`。      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Linux     |
| 如何在 DSVM 上配置/安装它？  | PyTorch 安装在 Python 3.5 (_py35_) 环境中。   |
| 示例链接      | 包括了示例 Jupyter Notebook，可以在 /dsvm/samples/pytorch 中找到示例。      |
| DSVM 上的相关工具      | Torch      |

### <a name="how-to-use--run-it"></a>如何使用/运行它？  

在终端，运行 _python_，然后导入 torch。 在 Jupyter 中，选择 Python 3.5 内核，然后导入 torch。


## <a name="mxnet-model-server"></a>MXNet 模型服务器

|    |           |
| ------------- | ------------- |
| 它是什么？   | 用于为 MXNet 和 ONNX 模型创建 HTTP 终结点的服务器      |
| 支持的 DSVM 版本      | Linux     |
| 如何在 DSVM 上配置/安装它？  | 在终端提供了 _mxnet-model-server_。   |
| 示例链接      | 在 [MXNet 模型服务器页面](https://github.com/awslabs/mxnet-model-server)上查找最新示例。    |
| DSVM 上的相关工具      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow 服务

|    |           |
| ------------- | ------------- |
| 它是什么？   | 在 TensorFlow 模型上运行推断的服务器      |
| 支持的 DSVM 版本      | Linux     |
| 如何在 DSVM 上配置/安装它？  | 在终端提供了 _tensorflow_model_server_。   |
| 示例链接      | 可以[联机](https://www.tensorflow.org/serving/)获取示例。      |
| DSVM 上的相关工具      | TensorFlow      |
