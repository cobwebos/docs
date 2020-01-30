---
title: 什么是 Azure 机器学习计算实例？
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习的计算实例，这是一个完全托管的基于云的工作站。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 7f7e0c3eef96970c1820783225a4ff59be8113b3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769099"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>什么是 Azure 机器学习计算实例？

Azure 机器学习计算实例（预览版）是一种完全托管的基于云的工作站，适用于数据科学家。 

利用计算实例，可以轻松开始 Azure 机器学习开发，并为 IT 管理员提供管理和企业就绪能力。  

使用计算实例作为云中的完全配置和托管开发环境。

计算实例通常用作开发环境。  它们还可用作定型和推断的计算目标，以进行开发和测试。  对于大型任务，具有多节点缩放功能的[Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选择。

> [!NOTE]
> 当前，计算实例仅适用于区域为**美国中北部**或**英国南部**的工作区，并对即将推出的其他区域提供支持。
>如果你的工作区在任何其他区域，则可以继续创建并使用[笔记本 VM](concept-compute-instance.md#notebookvm)。 

## <a name="why-use-a-compute-instance"></a>为什么使用计算实例？

计算实例是一种完全托管的基于云的工作站，适用于机器学习开发环境。 它提供以下优势：

|主要优点||
|----|----|
|工作效率|数据科学家可以使用集成的笔记本和其 web 浏览器中的以下工具来构建和部署模型：<br/>-Jupyter<br/>-JupyterLab<br/>-RStudio|
|托管 & 安全|降低安全性，并满足企业安全要求。 计算实例提供可靠的管理策略和安全网络配置，例如：<br/><br/>-从资源管理器模板或 Azure 机器学习 SDK 自动预配<br/>- [基于角色的访问控制（RBAC）](/azure/role-based-access-control/overview)<br/>- [虚拟网络支持](how-to-enable-virtual-network.md#compute-instance)<br/>-SSH 策略，用于启用/禁用 SSH 访问|
|预先配置的&nbsp;或&nbsp;ML|利用预配置的最新 ML 包、深度学习框架和 GPU 驱动程序，节省有关安装任务的时间。|
|完全自定义|广泛支持的 Azure VM 类型（包括 Gpu 和持久的低级自定义，例如安装包和驱动程序）使高级方案变得非常简单。 |

## <a name="contents"></a>工具和环境

Azure 机器学习计算实例，可以在工作区中以完全集成的笔记本体验创作、定型和部署模型。


这些工具和环境安装在计算实例上： 

|常规工具 & 环境|详细信息|
|----|:----:|
|驱动程序|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 库||
|Azure CLI ||
|Azure 机器学习示例 ||
|Azure 机器学习 EDAT 引擎 ||
|Docker||
|Nginx||
|NCCL 2。0 ||
|Protobuf|| 

|**R**工具 & 环境|详细信息|
|----|:----:|
|RStudio 服务器开源版||
|R 内核||
|适用于 R 的 Azure 机器学习 SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 示例|

|**PYTHON**工具 & 环境|详细信息|
|----|----|
|Anaconda Python||
|Jupyter 和扩展||
|Jupyterlab 和扩展||
|Visual Studio Code ||
[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>from PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|其他 PyPI 包|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 包|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|深度学习包|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 包|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure 机器学习 Python & R SDK 示例||

Python 包都安装在**python 3.6 AzureML**环境中。  

计算实例通常用作开发环境。  它们还可用作定型和推断的计算目标，以进行开发和测试。  对于大型任务，具有多节点缩放功能的[Azure 机器学习计算群集](how-to-set-up-training-targets.md#amlcompute)是更好的计算目标选择。

### <a name="installing-packages"></a>安装包

可以直接在 Jupyter 笔记本或 Rstudio 中安装包：

* RStudio 使用右下方的 "**包**" 选项卡，或左上角的 "**控制台**" 选项卡。  
* Python：添加安装代码并在 Jupyter 笔记本单元中执行。

也可以通过以下任一方式访问终端窗口：

* RStudio：选择左上方的 "**终端**" 选项卡。
* Jupyter Lab：在 "启动器" 选项卡中的 "**其他**" 标题下选择 "**终端**" 磁贴。
* Jupyter：在 "文件" 选项卡中，选择右上方的 "**新建 > 终端**"。
* 通过 SSH 连接到计算机。  然后，在**python 3.6 AzureML**环境中安装 python 包。  在**r**环境中安装 r 包。

## <a name="accessing-files"></a>访问文件

笔记本和 R 脚本存储在 Azure 文件共享中工作区的默认存储帐户中。  这些文件位于 "用户文件" 目录下。 通过此存储，可轻松地在计算实例之间共享笔记本。 当你停止或删除计算实例时，存储帐户还会使你的笔记本安全地保留下来。

你的工作区的 Azure 文件共享帐户将作为驱动器安装在计算实例上。 此驱动器是 Jupyter、Jupyter 实验室和 RStudio 的默认工作目录。

可以从同一工作区中的所有计算实例访问文件共享中的文件。 对计算实例上这些文件所做的任何更改都将可靠地保留回文件共享。

还可以将最新的 Azure 机器学习示例克隆到工作区文件共享中 "用户文件" 目录下的文件夹中。

比写入 VM 本身相比，在网络驱动器上写入小文件可能会更慢。  如果要编写许多小文件，请尝试直接在计算实例上使用目录，例如 `/tmp` 目录。 请注意，工作区中的其他计算实例将无法访问这些文件。

## <a name="managing-a-compute-instance"></a>管理计算实例

在 Azure 机器学习 studio 的工作区中，选择 "**计算**"，然后选择顶部的 "**计算实例**"。

![管理计算实例](./media/concept-compute-instance/manage-compute-instance.png)

你可以执行以下操作：

* 创建计算实例。 指定名称，包括 Gpu 的 Azure VM 类型（请注意，创建后无法更改 VM 类型）、启用/禁用 SSH 访问和配置虚拟网络设置（可选）。 你还可以直接从集成的笔记本、Azure 门户、资源管理器模板或 Azure 机器学习 SDK 创建实例。 适用于计算实例创建的每个区域配额专用核心是统一的，并与 Azure 机器学习计算群集配额共享。
* 刷新 "计算实例" 选项卡
* 启动、停止和重新启动计算实例
* 删除计算实例

对于工作区中的每个计算实例，可以：

* 访问计算实例上的 Jupyter、JupyterLab、RStudio、VS Code Uri
* 通过 SSH 连接到计算实例。 默认情况下，SSH 访问处于禁用状态，但可以在创建计算实例时启用。 SSH 访问通过公共/私有密钥机制。 该选项卡将提供 SSH 连接的详细信息，例如 IP 地址、用户名和端口号。
* 获取有关特定计算实例的详细信息，例如 IP 地址和区域。

[RBAC](/azure/role-based-access-control/overview)允许您控制工作区中的哪些用户可以创建、删除、启动、停止和重启计算实例。 "工作区参与者" 和 "所有者" 角色中的所有用户都可以在工作区中创建、删除、启动、停止和重启计算实例。 但是，只允许特定计算实例的创建者在该计算实例上访问 Jupyter、JupyterLab 和 RStudio。 计算实例的创建者具有专用的计算实例，具有根访问权限，可通过 Jupyter 进行终端。 计算实例将具有 creator 用户的单用户登录名，并且所有操作都将使用该用户的标识进行实验性和归属的运行。 SSH 访问是通过公钥/私钥机制控制的。

你还可以创建实例
* 直接从集成笔记本体验
* 在 Azure 门户中配置
* 从 Azure 资源管理器模板
* 与 Azure 机器学习 SDK

适用于计算实例创建的专用核心数配额统一，并与 Azure 机器学习定型群集配额共享。 

## <a name="compute-target"></a>计算目标

计算实例可用作[定型计算目标](concept-compute-target.md#train)，类似于 Azure 机器学习计算定型群集。 预配多 GPU VM，使用 TensorFlow/PyTorch 估算运行分布式培训作业。 你还可以创建运行配置，并使用它在计算实例上运行试验。 你可以使用计算实例作为测试/调试方案的本地推断部署目标。

## <a name="notebookvm"></a>笔记本 VM 发生了什么情况？

计算实例正在替换笔记本 VM。  在计算实例尚未提供的区域中，你可以继续使用具有完整功能的笔记本 Vm，并创建新的笔记本 Vm。

工作区数据存储区中存储的任何笔记本文件以及工作区数据存储区中的数据都可从计算实例访问。 但是，任何以前安装在笔记本 VM 上的自定义包都需要在计算实例上重新安装。 应用于计算群集创建的配额限制也适用于创建计算实例。 

在计算实例可用的区域中，无法创建新的笔记本 Vm。 不过，你仍然可以访问和使用已创建的笔记本 Vm，并具有完整功能。 可以在与现有笔记本 Vm 相同的工作区中创建计算实例。 


## <a name="next-steps"></a>后续步骤

 * [教程：训练首个 ML 模型](tutorial-1st-experiment-sdk-train.md)介绍如何将计算实例与集成笔记本一起使用。
