---
title: 深入了解 & AI 框架
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 上可用的深度学习框架和工具, 包括 TensorFlow、PyTorch、Keras、Caffe、MXNet、Horovod、Theano、Chainer 等。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: f71a2cc5c0a430037b429aac4f3ec4dc5f5f933d
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208108"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>适用于 Azure Data Science VM 的深度学习和 AI 框架
[Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) 支持许多深入学习的框架, 可帮助使用预测分析和认知功能 (如图像和语言理解) 构建人工智能 (AI) 应用程序。

通过 DSVM 提供的深度学习框架包括:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet 模型服务器
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Deep Water
+ NVIDIA DIGITS
+ nvidia-smi
+ TensorFlow 服务
+ TensorRT
+ Microsoft 认知工具包

|DSVM 上&nbsp;&nbsp;的&nbsp;深度学习工具|Windows|Linux|使用&nbsp;说明|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | 是 (Windows 2016) | 是 |在适用于[Linux 和 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)的 python 3.5 中安装, 在[windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上安装 python 3.6。 DSVM 上包含了示例 Jupyter 笔记本。<br/><br/>**若要运行**:<br/>最终激活正确的环境, 然后运行 Python。 <br/> Jupyter连接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后打开 TensorFlow 目录获取示例。  |
|[PyTorch](https://pytorch.org/)| 否 | 是 |在[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)中安装。 包含 Jupyter 笔记本示例, 其中的示例位于/dsvm/samples/pytorch。    <br/><br/>**若要运行**:<br/>最终激活正确的环境, 然后运行 Python。<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux):连接, 然后打开 PyTorch 目录获取示例。  |
|[Keras](https://keras.io/)| 是 | 是 |API 安装在适用于[Linux 和 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)的 python 3.5 中, 以及在[windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上的 python 3.6 中安装。 [请参阅示例](https://github.com/fchollet/keras/tree/master/examples)。<br/><br/>**若要运行**:<br/>最终激活正确的环境, 然后运行 Python。 <br/> Jupyter从 GitHub 位置下载示例, 连接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后打开示例目录。 |
|[Caffe](https://github.com/caffe2/caffe2) | 否 |是 (Ubuntu)|Caffe 在 `/opt/caffe` 中安装。   示例在中`/opt/caffe/examples`。 <br/><br/>**若要运行它**, 请使用 X2Go 登录到 VM, 然后启动新终端并输入以下内容:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>系统将打开显示示例笔记本的新浏览器窗口。 二进制文件安装在 /opt/caffe/build/install/bin 中。<br/><br/>安装的 Caffe 版本需要 Python 2.7, 并且不能与默认激活的 Python 3.5 一起使用。 若要切换到 Python 2.7, `source activate root`请运行以切换到 Anaconda 环境。|
|[Caffe2](https://github.com/caffe2/caffe2) | 否 |是 (Ubuntu)|Caffe2 安装在 [Python 2.7 (root) conda 环境](dsvm-languages.md#python-linux-and-windows-server-2012-edition)中。 源位于 `/opt/caffe2` 中。<br/>示例笔记本包含在 JupyterHub 中。<br/><br/>**若要运行**:<br/>最终激活[根 python 环境](dsvm-languages.md#python-linux-and-windows-server-2012-edition), 启动 Python, 并导入 Caffe2。 <br/> JupyterHub[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后中转到 Caffe2 目录查找示例笔记本。 某些笔记本需要在 Python 代码中设置 Caffe2 根；输入 /opt/caffe2。 |
|[Torch](http://torch.ch/) | 否 |是 (Ubuntu)|Torch 在 `/dsvm/tools/torch` 中安装。 PyTorch 在 Python 2.7 (_root_) 以及 Python 3.5 (_py35_) 环境中安装。 Torch 示例是 in `/dsvm/samples/torch`和 PyTorch 示例。 `/dsvm/samples/pytorch` |
|[MXNet](https://mxnet.io/) | 是 (Windows 2016) | 是|MXNet 在 Windows 上安装在 `C:\dsvm\tools\mxnet` 中，在 Linux 上安装在 `/dsvm/tools/mxnet` 中。 Python 绑定在 [Linux 和 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中，在 [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) 上安装于 Python 3.6 中。 Ubuntu 上同时还会安装 R 绑定。<br/><br/>包含 Jupyter 笔记本示例。 <br/><br/>**若要运行**:<br/>最终激活正确的环境, 然后运行 Python。 <br/> Jupyter连接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后打开 mxnet 目录获取示例。|
|[MXNet 模型服务器](https://github.com/awslabs/mxnet-model-server) | 否 | 是 |用于为 MXNet 和 ONNX 模型创建 HTTP 终结点的服务器。 _Mxnet-服务器_在终端上可用。 [MXNet 模型服务器页](https://github.com/awslabs/mxnet-model-server)上的示例。|
|[Horovod](https://github.com/uber/horovod) | 否 | 是 (Ubuntu) |适用于 TensorFlow 的分布式深入学习框架。 Horovod 在 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 上安装于 Python 3.5 中。  [请参阅示例](https://github.com/uber/horovod/tree/master/examples)。<br/><br/>**若要运行该示例**, 请在终端激活正确的环境, 然后运行 Python。 |
|[Theano](https://github.com/Theano/Theano) | 否 | 是 (Ubuntu) |Theano 安装在 Python 2.7 (_root_) 和 python 3.5 (_py35_) 环境中。<br/><br/>**若要运行**: <br/>最终激活所需的 Python 版本 (root 或 py35), 运行 Python, 然后导入 Theano。<br/>Jupyter选择 Python 2.7 或3.5 内核, 然后导入 Theano。  <br/>若要解决最近的数学内核库 (MKL) bug, 需要先设置 "MKL 线程" 层, 如下所示:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |否 | 是 |Chainer 安装在 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 中。 同时还会安装 ChainerRL 和 ChainerCV。 <br/><br/>示例笔记本包含在 JupyterHub 中。<br/><br/>**若要运行**: <br/>最终激活[python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)环境, 运行_python_, 然后导入 chainer。 <br/> JupyterHub[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后中转到 Chainer 目录查找示例笔记本。|
|[NVidia 数字](https://github.com/NVIDIA/DIGITS) | 否 | 是 (Ubuntu) |NVIDIA 的深度学习系统, 用于快速训练深度学习模型。 中`/dsvm/tools/DIGITS`安装了数字, 并将其作为名为的服务提供。  <br/><br/>**若要运行**: <br/>通过 X2Go 登录到 VM。 在终端上, 通过运行```sudo systemctl start digits```启动服务。 <br/><br/>该服务需要约一分钟启动。 打开 Web 浏览器并转到 `http://localhost:5000`。 请注意，DIGITS 不提供安全登录，不应在 VM 外部公开。|
|[CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit) |是 | 是 | |
|nvidia-smi|是 | 是 |用于查询 GPU 活动的 NVIDIA 工具;_nvidia-smi-s_在系统路径上可用。 <br/><br/>打开命令提示符 (在 Windows 上) 或终端 (在 Linux 上), 然后运行_nvidia-smi_。|
|[TensorFlow 服务](https://www.tensorflow.org/serving/) | 否 | 是 |要在 TensorFlow 模型上推断的服务器;终端上提供了 tensorflow_model_server。 可以[联机](https://www.tensorflow.org/serving/)获取示例。|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  否 | 是 (Ubuntu) |NVIDIA 的深度学习推理服务器。 TensorRT 作为 _apt_ 包安装。 可以[联机](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)获取示例。|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|是 | 是 | 在适用于[Linux 和 windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)的 python 3.5 中安装, 在[windows 2016](dsvm-languages.md#python-windows-server-2016-edition)上安装 python 3.6。 DSVM 上包含了示例 Jupyter 笔记本。 <br/><br/>**若要运行**: <br/>最终激活正确的环境, 然后运行 Python。 <br/>Jupyter连接到[Jupyter](provision-vm.md)或[JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后打开 CNTK 目录获取示例。 |
|Deep Water|否 | 是 (Ubuntu) |H2O 的深入学习框架, Deep 水源安装在[Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)中, 也可在中`/dsvm/tools/deep_water`使用。 示例笔记本包含在 JupyterHub 中。 Deep Water 需要 CUDA 8 与 cuDNN 5.1。 默认情况下, 这不在库路径中, 因为其他深入学习框架使用 CUDA 9 和 cuDNN 7。 若要使用, 请安装 CUDA 8 + cuDNN 5.1 for Deep 水源:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>使用 Deep Water：<br/>最终激活[python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)环境, 然后运行_python_。 <br/>JupyterHub[连接到 JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), 然后中转到 deep_water 目录查找示例笔记本。|
