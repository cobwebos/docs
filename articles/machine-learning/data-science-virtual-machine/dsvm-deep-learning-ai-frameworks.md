---
title: 深入了解 & AI 框架
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 上的可用深入学习框架和工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: fd38bf1f7741c4d610ef43a12d90533d4ac7b703
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802408"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>适用于 Azure Data Science VM 的深度学习和 AI 框架
下面列出了 DSVM 上的深度学习框架。

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| 支持的版本 | |
| 支持的 DSVM 版本      | Linux (Ubuntu)     |
| 如何在 DSVM 上配置/安装它？  | Caffe 在 `/opt/caffe` 中安装。   示例在中`/opt/caffe/examples`。|
| 如何运行      | 使用 X2Go 登录到 VM，然后启动新终端并输入以下内容：<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>系统将打开显示示例笔记本的新浏览器窗口。 二进制文件安装在 /opt/caffe/build/install/bin 中。<br/><br/>安装的 Caffe 版本需要 Python 2.7，并且不能与默认激活的 Python 3.5 一起使用。 若要切换到 Python 2.7， `source activate root`请运行以切换到 Anaconda 环境。|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| 支持的版本 | |
| 支持的 DSVM 版本      | Linux (Ubuntu)     |
| 如何在 DSVM 上配置/安装它？  | Caffe2 安装在 [Python 2.7 （root） conda 环境中。 |
| 如何运行      | 最终启动 Python，并导入 Caffe2。 <br/> JupyterHub[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后中转到 Caffe2 目录查找示例笔记本。 某些笔记本需要在 Python 代码中设置 Caffe2 根；输入 /opt/caffe2。 |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 5.2 |
| 支持的 DSVM 版本      | Linux (Ubuntu)     |
| 如何在 DSVM 上配置/安装它？  | Chainer 安装在 Python 3.5。 |
| 如何运行      | 最终激活 Python 3.5 环境，运行 `python`，然后 `import chainer`。 <br/> JupyterHub[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后中转到 Chainer 目录查找示例笔记本。| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 10.0.130|
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  |_nvidia-smi_ 在系统路径上可用。  |
| 如何运行      | 打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行_nvidia-smi_。 |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 0.16.1|
| 支持的 DSVM 版本      | Linux (Ubuntu)   |
| 如何在 DSVM 上配置/安装它？  | Horovod 安装在 Python 3.5 中 |
| 如何运行      | 在终端激活正确的环境，然后运行 Python。 |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 2.2.4 |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  | Keras 安装在 Windows 上的 Python 3.6 和 Linux 的 Python 3.5 中 |
| 如何运行      | 在终端激活正确的环境，然后运行 Python。 |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit （CNTK）](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 2.5.1 |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  | CNTK 安装在[Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3.6 中，在[Linux](./dsvm-languages.md#python-linux-edition)上安装 python 3.5） |
| 如何运行      | 最终激活正确的环境，然后运行 Python。 <br/>Jupyter连接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后打开 CNTK 目录获取示例。 |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.2.0 |
| 支持的 DSVM 版本      | Linux |
| 如何在 DSVM 上配置/安装它？  | 在[Python 3.5](dsvm-languages.md#python-linux-edition)中安装。 包含 Jupyter 笔记本示例，其中的示例位于/dsvm/samples/pytorch。 |
| 如何运行      | 最终激活正确的环境，然后运行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)：连接，然后打开 PyTorch 目录获取示例。  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.13 |
| 支持的 DSVM 版本      | Windows、Linux |
| 如何在 DSVM 上配置/安装它？  | 在[Linux](dsvm-languages.md#python-linux-edition)上的 python 3.5 中安装，在[Windows](dsvm-languages.md#python-windows-server-2016-edition)上安装 python 3.6 2016 |
| 如何运行      | 最终激活正确的环境，然后运行 Python。 <br/> Jupyter连接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)，然后打开 TensorFlow 目录获取示例。   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[TensorFlow 服务](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.12 |
| 支持的 DSVM 版本      | Linux |
| 如何在 DSVM 上配置/安装它？  | 终端上提供了 tensorflow_model_server。 |
| 如何运行      |  可以[联机](https://www.tensorflow.org/serving/)获取示例。   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.0.3 |
| 支持的 DSVM 版本      | Linux |
| 如何在 DSVM 上配置/安装它？  |Theano 安装在 Python 2.7 （_root_）和 python 3.5 （_py35_）环境中。 |
| 如何运行      |  最终激活所需的 Python 版本（root 或 py35），运行 Python，然后导入 Theano。<br/>Jupyter选择 Python 2.7 或3.5 内核，然后导入 Theano。  <br/>若要解决最近的数学内核库（MKL） bug，需要先设置 "MKL 线程" 层，如下所示：<br/><br/>`export MKL_THREADING_LAYER=GNU`  |