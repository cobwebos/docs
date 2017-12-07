---
title: "如何将 GPU 用于 Azure 机器学习 | Microsoft Docs"
description: "本文介绍如何在 Azure 机器学习工作台中使用图形处理单元 (GPU) 训练深度神经网络。"
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 4ef5c9ad58f86a669fc5c0bceb085ef2a266a008
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>如何在 Azure 机器学习中使用 GPU
图形处理单元 (GPU) 广泛用于处理通常在训练某些深度神经网络模型时发生的计算密集型任务。 使用 GPU 可以显著减少模型的训练时间。 在本文档中，可了解如何配置 Azure ML 工作台，以使用配备 GPU（作为执行目标）的 [DSVM（数据科学虚拟机）](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)。 

## <a name="prerequisites"></a>先决条件
- 若要分步完成此操作方法指南，需要先[安装 Azure ML 工作台](quickstart-installation.md)。
- 需要能够访问配备 NVidia GPU 的计算机。
    - 可以在配备 GPU 的本地计算机（Windows 或 macOS）上直接运行脚本。
    - 还可以在配备 GPU 的计算机的 Docker 容器中运行脚本。

## <a name="execute-in-local-environment-with-gpus"></a>在配备 GPU 的“本地”环境中执行
可以在配备 GPU 的计算机上安装 Azure ML 工作台，并针对“本地”环境执行。 这可以是：
- 物理 Windows 或 macOS 计算机。
- Windows VM（虚拟机），例如使用 Azure NC 系列的 VM 模板预配的 Windows DSVM。

在这种情况下，Azure ML 工作台无需特殊配置。 只需确保在本地安装了所有所需的驱动程序、工具包和已启用 GPU 的机器学习库。 只需针对在 Python 运行时可以直接访问本地 GPU 硬件的“本地”环境执行。

1. 打开 AML 工作台。 转到“文件”和“打开命令提示符”。 
2. 在命令行中，安装已启用 GPU 的深度学习框架，例如 Microsoft 认知工具包、TensorFlow 等。例如：

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. 编写利用深度学习库的 Python 代码。
4. 选择“本地”作为计算环境，并执行 Python 代码。

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>在配备 GPU 的 Linux VM 上的“docker”环境中执行
Azure ML 工作台还支持在 Azure Linux VM 的 Docker 中执行。 用户在此具有一个实用选项，可以在一部分功能强大的虚拟硬件上运行计算密集型作业，并在运行结束时将其关闭以仅按使用情况付费。 虽然原则上可以在任何 Linux 虚拟机上使用 GPU，但基于 Ubuntu 的 DSVM 随附所需的 CUDA 驱动程序并预装了库，这简化了安装步骤。 请按照以下步骤操作：

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>在 Azure 中创建基于 Ubuntu 的 Linux 数据科学虚拟机
1. 打开 Web 浏览器，并转到 [Azure 门户](https://portal.azure.com)

2. 选择门户左侧的“+ 新建”。

3. 在商城中搜索“用于 Linux (Ubuntu) 的数据科学虚拟机”。

4. 单击“创建”以创建 Ubuntu DSVM。

5. 使用所需信息填写“基础知识”窗体。
在选择 VM 的位置时，请注意，GPU VM 仅适用于某些 Azure 区域，例如“美国中南部”。 请参阅[按区域提供的计算产品](https://azure.microsoft.com/en-us/regions/services/)。
单击“确定”以保存“基础知识”信息。

6. 选择虚拟机的大小。 选择一个包含以 NC 为前缀的 VM 的大小，因为此类虚拟机配备了 NVidia GPU 芯片。  单击“全部查看”，根据需要查看完整列表。 了解关于[配备了 GPU 的 Azure VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu) 的详细信息。

7. 完成剩余的设置，并查看购买信息。 单击“购买”以创建 VM。 记下分配给虚拟机的 IP 地址。 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>在 Azure ML 工作台中创建新项目 
可以使用“使用 TensorFlow 分类 MNIST”示例或“使用 CNTK 分类 MNIST 数据集”示例。

### <a name="create-a-new-compute-target"></a>创建新计算目标
从 Azure ML 工作台启动命令行。 输入以下命令。 使用自己的姓名、IP 地址、用户名和密码值替换下面示例中的占位符文本。 

```batch
C:\MyProj> az ml computetarget attach remotedocker --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" 
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>配置 Azure ML 工作台以访问 GPU
返回项目，打开“文件视图”，然后点击“刷新”按钮。 此时可以看到两个新配置文件 `my_dsvm.compute` 和 `my_dsvm.runconfig`。
 
打开 `my_dsvm.compute`。 将 `baseDockerImage` 更改为 `microsoft/mmlspark:plus-gpu-0.7.9`，并添加新行 `nvidiaDocker: true`。 如此，该文件应拥具有以下两行：
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9
nvidiaDocker: true
```
 
现在打开 `my_dsvm.runconfig`，将 `Framework` 值从 `PySpark` 更改为 `Python`。 如果未看到此行，请添加它，因为默认值是 `PySpark`。

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>引用深度学习框架 
打开 `conda_dependencies.yml` 文件，确保在使用 GPU 版本的深度学习框架 Python 程序包。 示例项目列出了 CPU 版本，因此需要进行此项更改。

TensorFlow 的示例： 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Microsoft 认知工具包的示例：
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

还可以使用 1 位 SGD 版本的 Microsoft 认知工具包，它可以改进多 GPU VM 的性能。 请注意 [1 位 SGD 的许可要求](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license)。

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>执行
此时已准备好运行 Python 脚本。 可以使用 `my_dsvm` 上下文在 Azure ML 工作台中运行它们，也可以使用命令行启动它：
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
若要验证是否已使用 GPU，请检查运行输出来查看如下内容：

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

祝贺你！ 你的脚本刚刚通过 Docker 容器利用了 GPU 的强大功能！

## <a name="next-steps"></a>后续步骤
在 Azure ML 库中查看使用 GPU 加速深度神经网络训练的示例。
