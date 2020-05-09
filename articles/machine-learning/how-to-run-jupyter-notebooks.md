---
title: 如何在工作区中运行 Jupyter 笔记本
titleSuffix: Azure Machine Learning
description: 了解如何在不将工作区保留在 Azure 机器学习 studio 中的情况下运行 Jupyter Notebook。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 67da2cb31d59838bb3ad2b964530d85d8be9be4c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783651"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>如何在工作区中运行 Jupyter 笔记本
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何直接在 Azure 机器学习 studio 中的工作区中运行 Jupyter 笔记本。 尽管可以启动[Jupyter](https://jupyter.org/)或[JupyterLab](https://jupyterlab.readthedocs.io)，但也可以在不离开工作区的情况下编辑和运行笔记本。

了解如何执行以下操作：

* 在工作区中创建 Jupyter 笔记本
* 从笔记本运行试验
* 更改笔记本环境
* 查找用于运行笔记本的计算实例的详细信息

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://aka.ms/AMLFree)。
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="create-notebooks"></a><a name="create"></a>创建笔记本

在 Azure 机器学习工作区中，创建一个新的 Jupyter 笔记本并开始工作。 新创建的笔记本存储在默认的工作区存储区中。 此笔记本可与具有工作区访问权限的任何人共享。 

若要创建新笔记本： 

1. 在[Azure 机器学习 studio](https://ml.azure.com)中打开工作区。
1. 在左侧选择 "**笔记本**"。 
1. 选择 "**我的文件**" 部分中列出的**用户文件**上方的 "新建**文件**" 图标。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="创建新文件":::

1. 命名该文件。 
1. 对于 Jupyter Notebook 文件，请选择 " **Python 笔记本**" 作为文件类型。
1. 选择文件目录。
1. 选择“创建”。 

> [!TIP]
> 也可以创建文本文件。  选择**文本**作为文件类型并将扩展名添加到名称（例如，myfile.py 或 myfile.txt）  

你还可以通过 "笔记本" 页顶部的工具上传文件夹和文件，包括笔记本。  笔记本和大多数文本文件类型显示在 "预览" 部分中。  任何其他文件类型都不提供预览版。

### <a name="clone-samples"></a>克隆示例

你的工作区包含一个包含笔记本的**示例**文件夹，旨在帮助你探索 SDK，并作为你自己的机器学习项目的示例。  你可以将这些笔记本克隆到你自己的工作区存储容器上的文件夹中。  

有关示例，请参阅[教程：创建首个 ML 试验](tutorial-1st-experiment-sdk-setup.md#azure)。

### <a name="a-nameterminal-use-files-from-git-and-version-my-files"></a><a name="terminal">使用 Git 中的文件和文件版本

您可以使用终端窗口访问所有 Git 操作。 所有 Git 文件和文件夹将存储在你的工作区文件系统中。

> [!NOTE]
> 将文件和文件夹添加到 **~/cloudfiles/code/Users**文件夹下的任何位置，使其在所有 Jupyter 环境中都可见。

访问终端：

1. 在[Azure 机器学习 studio](https://ml.azure.com)中打开工作区。
1. 在左侧选择 "**笔记本**"。
1. 选择左侧位于**用户文件**部分的任何笔记本。  如果没有任何笔记本，请先[创建笔记本](#create)
1. 选择**计算**目标或创建一个新的目标，并等待它运行。
1. 选择 "**打开终端**" 图标。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="打开终端":::

1. 如果看不到该图标，请选择计算目标右侧的 " **...** "，然后选择 "**打开终端**"。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="打开终端 .。。":::


详细了解如何[将 Git 存储库克隆到你的工作区文件系统中](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。


### <a name="share-notebooks-and-other-files"></a>共享笔记本和其他文件

复制并粘贴 URL 以共享笔记本或文件。  只有工作区的其他用户能够访问此 URL。  详细了解[如何授予对工作区的访问权限](how-to-assign-roles.md)。

## <a name="edit-a-notebook"></a>编辑笔记本

若要编辑笔记本，请打开位于工作区的**用户文件**部分的任何笔记本。 单击要编辑的单元格。 

运行的计算实例正在运行时，您还可以在任何 Python 笔记本中使用[Intellisense](https://code.visualstudio.com/docs/editor/intellisense)支持的代码完成功能。

还可以从笔记本工具栏启动 Jupyter 或 JupyterLab。  Azure 机器学习不提供来自 Jupyter 或 JupyterLab 的更新和修复 bug，因为它们是位于 Microsoft 支持部门边界之外的开放源产品。

### <a name="useful-keyboard-shortcuts"></a>有用的键盘快捷方式

|Keyboard  |操作  |
|---------|---------|
|Shift+Enter     |  运行单元       |
|Ctrl + M （Windows）     |  启用/禁用笔记本中的选项卡陷印。       |
|Ctrl + Shift + M （Mac & Linux）     |    启用/禁用笔记本中的选项卡陷印。     |
|选项卡（启用 tab 键捕获时） | 添加 "\t" 字符（缩进）
|选项卡（禁用 tab 键捕获时） | 将焦点更改到下一个可设定项（删除单元格按钮、运行按钮等）

## <a name="delete-a-notebook"></a>删除笔记本

*不能*删除**示例**笔记本。  这些笔记本是工作室的一部分，每次发布新的 SDK 时都会进行更新。  

*可以*通过以下任一方式删除**用户文件**笔记本：

* 在工作室中，选择文件夹或文件末尾的 " **...** "。  请确保使用受支持的浏览器（Microsoft Edge、Chrome 或 Firefox）。
* 从任何笔记本工具栏中，选择 "[**打开终端**](#terminal)" 以访问计算实例的终端窗口。
* 在 Jupyter 或 JupyterLab 中的工具。

## <a name="run-an-experiment"></a>运行试验

若要从笔记本运行试验，请先连接到正在运行的[计算实例](concept-compute-instance.md)。 如果没有计算实例，请使用以下步骤创建一个： 

1. 在**+** 笔记本工具栏中选择。 
2. 将计算命名为，并选择**虚拟机大小**。 
3. 选择“创建”。 
4. 计算实例自动连接到笔记本，你现在可以运行单元。

只有你可以查看和使用你创建的计算实例。  **用户文件**与 VM 分开存储，并在工作区中的所有计算实例之间共享。

### <a name="view-logs-and-output"></a>查看日志和输出

使用[笔记本小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)来查看运行和日志的进度。 小组件是异步的，并在训练结束之前提供更新。 Jupyter 和 JupterLab 也支持 Azure 机器学习小组件。

## <a name="change-the-notebook-environment"></a>更改笔记本环境

笔记本工具栏使你可以更改笔记本的运行环境。  

这些操作不会更改笔记本状态或笔记本中任何变量的值：

|操作  |结果  |
|---------|---------| --------|
|停止内核     |  停止任何正在运行的单元格。 运行单元会自动重新启动内核。 |
|导航到另一个工作区部分     |     停止运行单元。 |

这些操作将重置笔记本状态，并将重置笔记本中的所有变量。

|操作  |结果  |
|---------|---------| --------|
| 更改内核 | 笔记本使用新内核 |
| 切换计算    |     笔记本自动使用新的计算。 |
| 重置计算 | 当你尝试运行单元格时重新启动 |
| 停止计算     |    不会运行任何单元  |
| 在 Jupyter 或 JupyterLab 中打开笔记本     |    笔记本在新选项卡中打开。  |

### <a name="add-new-kernels"></a>添加新内核

笔记本会自动查找连接的计算实例上安装的所有 Jupyter 内核。  向计算实例添加内核：

1. 选择笔记本工具栏中的 "[**打开终端**](#terminal)"。
1. 使用终端窗口创建新环境。
1. 激活该环境。  例如，在创建`newenv`后：

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

可以安装任何[可用的 Jupyter 内核](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)。

### <a name="status-indicators"></a>状态指示器

"**计算**" 下拉列表旁边的指示器显示其状态。  状态也会显示在下拉列表中。  

|颜色 |计算状态 |
|---------|---------| 
| 绿色 | 计算正在运行 |
| Red |计算失败 | 
| 黑色 | 计算已停止 |
|  浅蓝色 |计算创建、启动、重新启动、设置 |
|  灰色 |计算删除、停止 |

**内核**下拉列表旁边的指示器显示其状态。

|颜色 |内核状态 |
|---------|---------|
|  绿色 |内核已连接、空闲、繁忙|
|  灰色 |未连接内核 |

## <a name="find-compute-details"></a>查找计算详细信息 

在[工作室](https://ml.azure.com)中的**计算**页上查找有关计算实例的详细信息。

## <a name="next-steps"></a>后续步骤

* [运行你的第一个试验](tutorial-1st-experiment-sdk-train.md)
* [备份文件存储和快照](../storage/files/storage-snapshots-files.md)
