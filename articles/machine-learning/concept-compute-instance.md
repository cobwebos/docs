---
title: 什么是 Azure 机器学习计算实例？
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习计算实例，这是一个完全托管的基于云的工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 8c03df8fb0cd8f5f092450ebe4c66266d2ff4293
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83816346"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什么是 Azure 机器学习计算实例？

Azure 机器学习计算实例（预览版）是面向数据科学家的完全托管的基于云的工作站。 

计算实例可让客户轻松地开始进行 Azure 机器学习开发，并为 IT 管理员提供管理和企业就绪功能。  

可将计算实例用作云中的完全配置、完全托管的开发环境。

计算实例通常用作开发环境。  它们还可用作训练和推理的计算目标来实现开发和测试目的。  对于大型任务，具有多节点缩放功能的 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选择。


## <a name="why-use-a-compute-instance"></a>为什么要使用计算实例？

计算实例是一个完全托管的基于云的工作站，已针对机器学习开发环境进行优化。 它具有以下优点：

|主要优点||
|----|----|
|工作效率|数据科学家可以在其 Web 浏览器中使用集成笔记本和以下工具来构建和部署模型：<br/>- Jupyter<br/>- JupyterLab<br/>- RStudio|
|采用托管服务且安全|减少安全保护工作，增强企业的安全要求合规性。 计算实例提供可靠的管理策略和安全的网络配置，例如：<br/><br/>- 通过资源管理器模板或 Azure 机器学习 SDK 自动预配<br/>- [基于角色的访问控制 (RBAC)](/azure/role-based-access-control/overview)<br/>- [虚拟网络支持](how-to-enable-virtual-network.md#compute-instance)<br/>- 用于启用/禁用 SSH 访问的 SSH 策略|
|预先经过配置&nbsp;或&nbsp;支持 ML|使用预配置的最新 ML 包、深度学习框架和 GPU 驱动程序完成设置任务，可节省时间。|
|完全可自定义|支持多种 Azure VM 类型，包括 GPU 和持久性低级自定义，例如，安装相应的包和驱动程序可以轻而易举地实现高级方案。 |

## <a name="tools-and-environments"></a><a name="contents"></a> 工具和环境

使用 Azure 机器学习计算实例可以在工作区中的完全集成式笔记本体验中创作、训练和部署模型。


计算实例上安装了以下工具和环境： 

|常规工具和环境|详细信息|
|----|:----:|
|驱动程序|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 库||
|Azure CLI ||
|Azure 机器学习示例 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|R 工具和环境|详细信息|
|----|:----:|
|RStudio Server 开放源代码版本||
|R 内核||
|适用于 R 的 Azure 机器学习 SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 示例|

|Python 工具和环境|详细信息|
|----|----|
|Anaconda Python||
|Jupyter 和扩展||
|Jupyterlab 和扩展||
[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>（来自 PyPI）|包括大多数 azureml 额外包。  若要查看完整列表，请[打开计算实例上的终端窗口](how-to-run-jupyter-notebooks.md#terminal)并运行 <br/> `conda list -n azureml_py36 azureml*` |
|其他 PyPI 包|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 包|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度学习包|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 包|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure 机器学习 Python 和 R SDK 示例||

Python 包都安装在“Python 3.6 - AzureML”环境中。  

计算实例通常用作开发环境。  它们还可用作训练和推理的计算目标来实现开发和测试目的。  对于大型任务，具有多节点缩放功能的 [Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选择。

### <a name="installing-packages"></a>安装包

可以直接在 Jupyter 笔记本或 Rstudio 中安装包：

* RStudio 使用右下角的“包”选项卡，或左上角的“控制台”选项卡 。  
* Python:添加安装代码并在 Jupyter 笔记本单元中执行。

也可以通过以下任一方式访问终端窗口：

* RStudio：选择左上角的“终端”选项卡。
* Jupyter 实验室：在“启动程序”选项卡的“其他”标题下选择“终端”磁贴 。
* Jupyter：在“文件”选项卡的右上角选择“新建”>“终端”。
* 通过 SSH 连接到计算机。  然后将 Python 包安装到“Python 3.6 - AzureML”环境中。  将 R 包安装到“R”环境中。

## <a name="accessing-files"></a>访问文件

笔记本和 R 脚本存储在 Azure 文件共享中工作区的默认存储帐户中。  这些文件位于“用户文件”目录下。 这种存储使得在计算实例之间共享笔记本变得很容易。 停止或删除计算实例时，存储帐户还会安全保存笔记本。

工作区的 Azure 文件共享帐户作为驱动器安装在计算实例上。 此驱动器是 Jupyter、Jupyter 实验室和 RStudio 的默认工作目录。

可以从同一工作区中的所有计算实例访问文件共享中的文件。 对计算实例上这些文件的任何更改都将可靠地持久保存回文件共享。

还可以将最新的 Azure 机器学习示例克隆到工作区文件共享中“用户文件”目录下的文件夹内。

比写入到 VM 本身相比，在网络驱动器上写入小文件可能速度更慢。  如果要编写许多小文件，请尝试直接在计算实例上使用目录，例如 `/tmp` 目录。 请注意，无法从工作区中的其他计算实例访问这些文件。

## <a name="managing-a-compute-instance"></a>管理计算实例

在 Azure 机器学习工作室的工作区中，选择“计算”，然后选择顶部的“计算实例” 。

![管理计算实例](./media/concept-compute-instance/manage-compute-instance.png)

可执行以下操作：

* 创建计算实例。 指定名称、包括 GPU 在内的 Azure VM 类型（请注意，VM 类型在创建后不能更改）、启用/禁用 SSH 访问以及配置虚拟网络设置（可选）。 也可直接从集成笔记本、Azure 门户、资源管理器模板或 Azure 机器学习 SDK 创建实例。 创建计算实例时应用的“每个区域的专用核心数”配额是统一的，并与 Azure 机器学习计算群集配额共享。
* 刷新“计算实例”选项卡
* 启动、停止和重启计算实例。 不使用 VM 时，请将其停止，以便降低成本。 然后在需要时重启。
* 删除计算实例

对于工作区中的每个计算实例，可以：

* 访问计算实例上的 Jupyter、JupyterLab、RStudio
* 通过 SSH 连接到计算实例。 SSH 访问在默认情况下处于禁用状态，但可以在计算实例创建时启用。 SSH 访问通过公钥/私钥机制实现。 选项卡中将提供 SSH 连接的详细信息，如 IP 地址、用户名和端口号。
* 获取有关特定计算实例的详细信息，如 IP 地址和区域。

使用 [RBAC](/azure/role-based-access-control/overview) 可以控制工作区中的哪些用户可以创建、删除、启动、停止和重启计算实例。 工作区参与者和所有者角色中的所有用户都可以跨工作区创建、删除、启动、停止和重启计算实例。 但是，只有特定计算实例的创建者才可访问该计算实例上的 Jupyter、JupyterLab 和 RStudio。 计算实例的创建者拥有专用的计算实例，具有根访问权限，且可通过 Jupyter 进入终端。 计算实例具有创建者用户的单用户登录名，所有操作将使用该用户的标识进行试验运行的 RBAC 控制和权限划分。 SSH 访问通过公钥/私钥机制进行控制。

也可通过以下方式创建实例
* 直接从集成式笔记本体验
* 在 Azure 门户中配置
* 通过 Azure 资源管理器模板
* 使用 Azure 机器学习 SDK

创建计算实例时应用的“每个区域的专用核心数”配额是统一的，并与 Azure 机器学习训练群集配额共享。 

## <a name="compute-target"></a>计算目标

计算实例可用作类似于 Azure 机器学习计算训练群集的[训练计算目标](concept-compute-target.md#train)。 预配多 GPU VM 可通过 TensorFlow/PyTorch 估算器运行分布式训练作业。 你还可创建运行配置，并使用它在计算实例上运行试验。 可将计算实例用作测试/调试方案的本地推理部署目标。

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a> Notebook VM 发生了什么情况？

计算实例即将取代 Notebook VM。  

任何存储在工作区文件共享中的笔记本文件和工作区数据存储中的数据都可以从计算实例访问。 但是，以前安装在笔记本 VM 上的任何自定义包都需要在计算实例上重新安装。 创建计算群集时适用的配额限制在创建计算实例时同样适用。 

无法创建新的 Notebook VM。 但你仍然可以访问和使用已创建的 Notebook VM 及其完整功能。 可以在现有 Notebook VM 所在的同一工作区中创建计算实例。 


## <a name="next-steps"></a>后续步骤

 * [教程：训练自己的首个 ML 模型](tutorial-1st-experiment-sdk-train.md)演示如何将计算实例与集成笔记本一起使用。
