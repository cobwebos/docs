---
title: 深度学习和人工智能框架
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 上可用的深度学习框架和工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270064"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>适用于 Azure Data Science VM 的深度学习和人工智能框架
下面列出了 DSVM 上的深度学习框架。

## <a name="caffe"></a>[卡夫](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| 支持的版本 | |
| 支持的 DSVM 版本      | Linux (Ubuntu)     |
| 如何在 DSVM 上配置/安装它？  | Caffe 在 `/opt/caffe` 中安装。   示例位于 `/opt/caffe/examples` 中。|
| 运行方式      | 使用 X2Go 登录 VM，然后启动新终端并输入以下内容：<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>系统将打开显示示例笔记本的新浏览器窗口。 二进制文件安装在 /opt/caffe/build/install/bin 中。<br/><br/>已安装的 Caffe 版本需要使用 Python 2.7，不会使用默认激活的 Python 3.5。 要切换到 Python 2.7，请运行 `source activate root` 以切换到 Anaconda 环境。|    

## <a name="caffe2"></a>[卡夫2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| 支持的版本 | |
| 支持的 DSVM 版本      | Linux (Ubuntu)     |
| 如何在 DSVM 上配置/安装它？  | Caffe2 安装在 [Python 2.7 (root) conda 环境中。 |
| 运行方式      | 终端：启动 Python 并导入 Caffe2。 <br/> • JupyterHub：[连接到JupyterHub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然后转到Caffe2目录查找示例笔记本。 某些笔记本需要在 Python 代码中设置 Caffe2 根；输入 /opt/caffe2。 |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 5.2 |
| 支持的 DSVM 版本      | Linux (Ubuntu)     |
| 如何在 DSVM 上配置/安装它？  | Chainer 安装在 Python 3.5 中。 |
| 运行方式      | 终端：激活 Python 3.5 环境`python`，运行，`import chainer`然后 。 <br/> • JupyterHub：[连接到JupyterHub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然后转到链子目录以查找示例笔记本。| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 10.0.130|
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  |_nvidia-smi_ 在系统路径上可用。  |
| 运行方式      | 打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 nvidia-smi__。 |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 0.16.1|
| 支持的 DSVM 版本      | Linux (Ubuntu)   |
| 如何在 DSVM 上配置/安装它？  | Horovod 安装在 Python 3.5 中 |
| 运行方式      | 在终端上激活正确的环境，然后运行 Python。 |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 2.2.4 |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  | Keras 在 Windows 上安装于 Python 3.6 中，在 Linux 上安装于 Python 3.5 中 |
| 运行方式      | 在终端上激活正确的环境，然后运行 Python。 |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 2.5.1 |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  | CNTK 在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中，在 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中 |
| 运行方式      | 终端：激活正确的环境并运行 Python。 <br/>朱比特：连接到[朱比特](provision-vm.md)或[朱比特Hub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然后打开CNTK目录的样本。 |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.3.0 |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  | MXNet 在 Windows 上安装于 `C:\dsvm\tools\mxnet` 中，在 Ubuntu 上安装于 `/dsvm/tools/mxnet` 中。 Python 绑定在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中，在 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中。Ubuntu DSVM 中也包含 R 绑定。 |
| 运行方式      | 终端：激活正确的 conda 环境，然后`import mxnet`运行 。 <br/>朱皮特：连接到[朱比特](provision-vm.md#access-the-dsvm)或[朱比特Hub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然后打开样本`mxnet`的目录。 |

## <a name="mxnet-model-server"></a>[MXNet 模型服务器](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.0.1 |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 如何在 DSVM 上配置/安装它？  | MXNet 模型服务器在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中，在 [Linux](./dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中 |
| 运行方式      | 终端：运行`sudo systemctl stop jupyterhub`以首先停止 JupyterHub 服务，因为两者都在同一端口上侦听。 然后，激活正确的 conda 环境，并运行 `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia 系统管理接口（nvidia-smi）](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| 支持的版本 |  |
| 支持的 DSVM 版本      | Windows 和 Linux   |
| 用途 | 用于查询 GPU 活动的 NVIDIA 工具 |
| 如何在 DSVM 上配置/安装它？  | `nvidia-smi` 位于系统路径上。 |
| 运行方式      | 在具有 GPU 的虚拟机上，打开命令提示符（在 Windows 上）或终端（在 Linux 上），然后运行 `nvidia-smi`****。 |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.2.0 （Ubuntu 16.04， Windows 2016）， 1.4.0 （Ubuntu 18.04， Windows 2019） |
| 支持的 DSVM 版本      | Linux |
| 如何在 DSVM 上配置/安装它？  | 安装在 [Python 3.5](dsvm-tools-languages.md#python-linux-edition) 中。 示例 Jupyter 笔记本包含在内，并且示例位于 /dsvm/samples/pytorch 中。 |
| 运行方式      | 终端：激活正确的环境，然后运行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)：连接，然后打开 PyTorch 目录以找到样本。  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.13 |
| 支持的 DSVM 版本      | Windows、Linux |
| 如何在 DSVM 上配置/安装它？  | 在 [Linux](dsvm-tools-languages.md#python-linux-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中 |
| 运行方式      | 终端：激活正确的环境，然后运行 Python。 <br/> • 朱比特：连接到[朱比特](provision-vm.md)或[朱比特Hub，](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)然后打开 TensorFlow 目录进行采样。   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.12 |
| 支持的 DSVM 版本      | Linux |
| 如何在 DSVM 上配置/安装它？  | 在终端提供了 tensorflow_model_server。 |
| 运行方式      |  可以[联机](https://www.tensorflow.org/serving/)获取示例。   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| 支持的版本 | 1.0.3 |
| 支持的 DSVM 版本      | Linux |
| 如何在 DSVM 上配置/安装它？  |Theano 安装在 Python 2.7 (root) 以及 Python 3.5 (py35) 环境中____。 |
| 运行方式      |  终端：激活所需的 Python 版本（根或 py35），运行 Python，然后导入 Theano。<br/>• 聚居器：选择 Python 2.7 或 3.5 内核，然后导入 Theano。  <br/>要解决最近的数学内核库 (MKL) bug，首先需要设置 MKL 线程层，如下所示：<br/><br/>`export MKL_THREADING_LAYER=GNU`  |