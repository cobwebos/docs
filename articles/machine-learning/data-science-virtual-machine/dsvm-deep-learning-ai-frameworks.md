---
title: 深度学习和人工智能框架 - Azure | Microsoft Docs
description: 深度学习和人工智能框架
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 891059a440189112c834f3402725781a6b4a3960
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952818"
---
# <a name="deep-learning-and-ai-frameworks"></a>深度学习和人工智能框架
[数据科学虚拟机](http://aka.ms/dsvm) (DSVM) 和[深度学习 VM](http://aka.ms/dsvm/deeplearning) 支持多个深度学习框架，可帮助生成具有预测分析和认知功能（例如图像和语言理解）的人工智能 (AI) 应用程序。 

下文详细介绍 DSVM 上可用的所有深度学习框架。

## <a name="microsoft-cognitive-toolkit"></a>Microsoft 认知工具包

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | Microsoft Cognitive Toolkit (CNTK) 在 [Linux 和 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中。   |
| 示例链接      | 包含 Jupyter 笔记本示例。     |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | *在终端上：激活正确的环境，然后运行 Python。 <br/>
 *在 Jupyter 中：连接到 [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后打开示例的 CNTK 目录。 |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | TensorFlow 在 [Linux 和 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中。  |
| 示例链接      | 包含 Jupyter 笔记本示例。     |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | *在终端上：激活正确的环境，然后运行 Python。 <br/>
 *在 Jupyter 中：连接到 [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后打开示例的 TensorFlow 目录。  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| 它是什么？   | TensorFlow 的分布式深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Horovod 在 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中。  |
| 示例链接      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| DSVM 上的相关工具      | TensorFlow      |
| 如何使用/运行它？    | 在终端上：激活正确的环境，然后运行 Python。 |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 它是什么？   | 高级别的深度学习 API      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | TensorFlow 在 [Linux 和 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中。 |
| 示例链接      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM 上的相关工具      | Microsoft 认知工具包、TensorFlow、Theano      |
| 如何使用/运行它？    | *在终端上：激活正确的环境，然后运行 Python。 <br/>
 *在 Jupyter 中：从 Github 位置下载示例，连接到 [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后打开示例目录。 |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Caffe 在 `/opt/caffe` 中安装。    |
| 如何切换到 Python 2.7 | 运行 `source activate root` |
| 示例链接      | `/opt/caffe/examples` 中包含示例。      |
| DSVM 上的相关工具      | Caffe2      |
### <a name="how-to-use--run-it"></a>如何使用/运行它？  

使用 X2Go 登录 VM，然后启动新终端并输入

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

系统将打开显示示例笔记本的新浏览器窗口。

二进制文件安装在 /opt/caffe/build/install/bin 中。

已安装的 Caffe 版本需要 Python 2.7，不会使用默认激活的 Python 3.5。 运行 `source activate root` 切换 Anaconda 环境。 

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Caffe2 安装在 [Python 2.7 (root) conda 环境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)中。 源位于 `/opt/caffe2` 中。 |
| 示例链接      | 示例笔记本包含在 JupyterHub 中。 |
| DSVM 上的相关工具      | Caffe      |
| 如何使用/运行它？    | *在终端上：激活[root Python 环境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)，启动 Python，然后导入 caffe2。 <br/> *在 JupyterHub 中：[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后导航到要查找示例笔记本的 Caffe2 目录。 某些笔记本需要在 Python 代码中设置 Caffe2 根；输入 /opt/caffe2。 |
| 版本说明 | Caffe2 从 Linux 上的源生成，并且包括 CUDA、cuDNN 和 Intel MKL。 当前提交的是 0d9c0d48c6f20143d6404b99cc568efd29d5a4be，此选择是为了实现所有 GPU 和测试示例上的稳定性。 |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | Chainer 安装在 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 中。 同时还会安装 ChainerRL 和 ChainerCV。   |
| 示例链接      | 示例笔记本包含在 JupyterHub 中。 |
| DSVM 上的相关工具      | Caffe      |
| 如何使用/运行它？  | *在终端上：激活 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 环境，运行 python，然后导入 Chainer。 <br/>
* 在 JupyterHub 中：[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后导航到要查找示例笔记本的 Chainer 目录。


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 它是什么？   | H2O 深度学习框架      |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | Deep Water 安装在 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 中，在 `/dsvm/tools/deep_water` 中也可用。   |
| 示例链接      | 示例笔记本包含在 JupyterHub 中。      |
| DSVM 上的相关工具      | H2O、Sparkling Water      |

### <a name="how-to-use--run-it"></a>如何使用/运行它？  

Deep Water 需要 CUDA 8 与 cuDNN 5.1。 默认情况下不位于库路径中，因为其他深度学习框架使用 CUDA 9 和 cuDNN 7。 对 Deep Water 使用 CUDA 8 + cuDNN 5.1：

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

使用 Deep Water：
* 在终端上：激活 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 环境，然后运行 _python_。 <br/>
* 在 JupyterHub 中：[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后导航到要查找示例笔记本的 deep_water 目录。

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 它是什么？   | 深度学习框架      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 如何在 DSVM 上配置/安装它？  | MXNet 在 Windows 上安装在 `C:\dsvm\tools\mxnet` 中，在 Linux 上安装在 `/dsvm/tools/mxnet` 中。 Python 绑定在 [Linux 和 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中。 Ubuntu 上同时还会安装 R 绑定。   |
| 示例链接      | 包含 Jupyter 笔记本示例。    |
| DSVM 上的相关工具      | Keras      |
| 如何使用/运行它？    | *在终端上：激活正确的环境，然后运行 Python。 <br/>
 *在 Jupyter 中：连接到 [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 或 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后打开示例的 mxnet 目录。  |
 | 版本说明 | MXNet 从 Linux 上的源生成。 此版本包括 CUDA、cuDNN、NCCL 和 MKL。 |

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



## <a name="nvidia-smi"></a>nvidia-smi

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
| 如何使用/运行它？    | *在终端上，激活所需的 Python 版本（root 或 py35），运行 python，然后导入 theano。 <br/> 
* 在 Jupyter 中，选择 Python 2.7 或 3.5 内核，然后导入 theano。  
<br/>
若要解决最近的 MKL bug，首先需要设置 MKL 线程层：<br/><br/>
_export MKL_THREADING_LAYER=GNU_
|



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
| 如何在 DSVM 上配置/安装它？  | PyTorch 安装在 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 中。  |
| 示例链接      | 包括了示例 Jupyter Notebook，可以在 /dsvm/samples/pytorch 中找到示例。      |
| DSVM 上的相关工具      | Torch      |
| 如何使用/运行它 | 
* 在终端上：激活正确的环境，然后运行 Python。 <br/>
 * 在 Jupyter 中：连接到 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)，然后打开示例的 PyTorch 目录。  |

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

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| 它是什么？   | NVIDIA 深度学习推理服务器。 |
| 支持的 DSVM 版本      | Ubuntu     |
| 如何在 DSVM 上配置/安装它？  | TensorRT 作为 _apt_ 包安装。   |
| 示例链接      | 可以[联机](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)获取示例。      |
| DSVM 上的相关工具      | TensorFlow 服务、MXNet 模型服务器  |



