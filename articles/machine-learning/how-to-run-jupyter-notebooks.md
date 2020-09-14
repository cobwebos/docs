---
title: 如何在工作区中运行 Jupyter 笔记本
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习工作室中的工作区中运行 Jupyter 笔记本。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 06/27/2020
ms.openlocfilehash: a59905c1d410ae0ffd4520f3b61fd37e649012e7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650923"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>如何在工作区中运行 Jupyter 笔记本
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何直接在 Azure 机器学习工作室中的工作区中运行 Jupyter 笔记本。 用户不但可以在工作区中启动 [Jupyter](https://jupyter.org/) 或 [JupyterLab](https://jupyterlab.readthedocs.io)，还可以直接编辑和运行笔记本。

本文介绍如何执行以下操作：

* 在工作区中创建 Jupyter 笔记本
* 在笔记本中运行试验
* 更改笔记本环境
* 查找用于运行笔记本的计算实例的详细信息

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://aka.ms/AMLFree)。
* 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="create-notebooks"></a><a name="create"></a> 创建笔记本

在 Azure 机器学习工作区中，创建一个新的 Jupyter 笔记本并开始运行。 新创建的笔记本存储在默认的工作区存储中。 有权访问此工作区的任何人都可以共享此笔记本。 

创建新笔记本的步骤： 

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中打开工作区。
1. 在左侧选择“笔记本”。 
1. 在“我的文件”部分的“用户文件”列表上方，选择“新建文件”图标。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="新建文件":::

1. 为文件命名。 
1. 对于 Jupyter Notebook 文件，请选择 " **笔记本** " 作为文件类型。
1. 选择文件目录。
1. 选择“创建” 。

也可以创建文本文件。  选择“文本”作为文件类型，并向名称添加扩展名（例如 myfile.py 或 myfile.txt）  

另外，还可以通过“笔记本”页顶部的工具上传文件夹和文件，包括笔记本。  笔记本和大多数文本文件类型可在“预览”部分显示。  其他大多数文件类型没有预览功能。

> [!IMPORTANT]
> 笔记本和脚本中的内容可能会从会话中读取数据，并在组织不在 Azure 中的情况下访问数据。  仅从受信任的源加载文件。 有关详细信息，请参阅[安全代码最佳做法](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)。

### <a name="clone-samples"></a>克隆示例

你的工作区包含一个“示例”文件夹，其中的笔记本旨在帮助你探索 SDK，并用作你自己的机器学习项目的示例。  可以将这些笔记本克隆到你自己工作区存储容器上的文件夹中。  

有关示例，请参阅[教程：创建第一个 ML 试验](tutorial-1st-experiment-sdk-setup.md#azure)。

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> 使用 Git 中的文件并对我的文件执行版本控制

可以使用终端窗口访问所有 Git 操作。 所有 Git 文件和文件夹都将存储在你的工作区文件系统中。

> [!NOTE]
> 将文件和文件夹添加到 ~/cloudfiles/code/Users 文件夹下的任意位置，使其在所有 Jupyter 环境中都可见。

访问终端的步骤：

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中打开工作区。
1. 在左侧选择“笔记本”。
1. 选择左侧“用户文件”部分中的任何笔记本。  如果没有笔记本，请先[创建笔记本](#create)
1. 选择 **计算** 目标或创建一个新的目标，并等待它运行。
1. 选择“打开终端”图标。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="打开终端":::

1. 如果看不到该图标，请选择计算目标右侧的 " **...** "，然后选择 " **打开终端**"。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="从“...”打开终端":::


详细了解如何[将 Git 存储库克隆到工作区文件系统](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)。


### <a name="share-notebooks-and-other-files"></a>共享笔记本和其他文件

复制并粘贴 URL 可以共享笔记本或文件。  只有工作区的其他用户可以访问此 URL。  详细了解如何[授权访问工作区](how-to-assign-roles.md)。

## <a name="edit-a-notebook"></a>编辑笔记本

若要编辑笔记本，请打开位于工作区“用户文件”部分的任何笔记本。 单击要编辑的单元格。 

你可以在不连接到计算实例的情况下编辑笔记本。  要运行笔记本中的单元格时，请选择或创建一个计算实例。  如果选择一个已停止的计算实例，则在你运行第一个单元时，该计算实例会自动启动。

如果计算实例正在运行，还可以在任何 Python Notebook 中使用由 [Intellisense](https://code.visualstudio.com/docs/editor/intellisense) 提供支持的代码完成功能。

另外，还可以从笔记本工具栏中启动 Jupyter 或 JupyterLab。  Azure 机器学习不提供来自 Jupyter 或 JupyterLab 的更新，也不修复其中的 bug，因为这些开源产品不在 Microsoft 支持的范围内。

### <a name="use-intellisense"></a>使用 IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) 是一个代码完成辅助插件，其中包括许多功能：“列出成员”、“参数信息”、“快速信息”和“完成单词”。 这些功能可帮助你详细了解所使用的代码、跟踪正在键入的参数，只需几次击键即可添加对属性和方法的调用。  

键入代码时，请使用“Ctrl+空格键”来触发 IntelliSense。

### <a name="clean-your-notebook-preview"></a>清理笔记本 (预览) 

> [!IMPORTANT]
> 收集功能目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在创建笔记本的过程中，通常最终会获得用于数据浏览或调试的单元格。 " *收集* " 功能可帮助你在不使用这些多余单元的情况下生成干净的笔记本。

1. 运行所有笔记本单元。
1. 选择包含想要新笔记本运行的代码的单元格。 例如，提交实验的代码或可能注册模型的代码。
1. 选择显示在 "单元格" 工具栏上的 " **集合** " 图标。
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="屏幕截图：选择 收集 图标":::
1. 输入新的 "收集的" 笔记本的名称。  

新笔记本只包含代码单元，所有单元格都需要生成与你为收集所选单元产生的结果相同的结果。

### <a name="save-and-checkpoint-a-notebook"></a>将笔记本保存并设置检查点

Azure 机器学习在创建 *ipynb*文件时创建一个检查点文件   。

在笔记本工具栏中，选择“菜单”，然后选择“文件”&gt;“保存并设置检查点”以手动保存笔记本，它会添加与笔记本关联的检查点文件。

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="笔记本工具栏中保存工具的屏幕截图":::

每个笔记本每隔30秒自动保存一次。自动保存仅更新初始 *ipynb*   文件，而不更新检查点文件。
 
在“笔记本”菜单中选择“检查点”以创建命名检查点，并将笔记本还原为已保存的检查点。


### <a name="useful-keyboard-shortcuts"></a>有用的键盘快捷方式

|键盘  |操作  |
|---------|---------|
|Shift+Enter     |  运行单元格       |
|Ctrl+空格键 | 激活 IntelliSense |
|Ctrl+M(Windows)     |  启用/禁用笔记本中的 Tab 键捕获。       |
|Ctrl+Shift+M（Mac 和 Linux）     |    启用/禁用笔记本中的 Tab 键捕获。     |
|Tab（Tab 键捕获处于启用状态） | 添加“\t”字符（缩进）
|Tab（Tab 键捕获处于禁用状态） | 将焦点更改为下一个可聚焦项（“删除单元格”按钮、“运行”按钮等）

## <a name="delete-a-notebook"></a>删除笔记本

不能删除“示例”笔记本。  这些笔记本是工作室的一部分，在每次发布新的 SDK 时，它们都会相应地获得更新。  

可以通过以下任一方式删除“用户文件”笔记本：

* 在工作室中，选择文件夹或文件末尾的“...”。  请确保使用支持的浏览器（Microsoft Edge、Chrome 或 Firefox）。
* 在任何笔记本工具栏中，选择”[打开终端](#terminal)”，访问计算实例的终端窗口。
* 在 Jupyter 或 JupyterLab 中使用自带工具删除。

## <a name="run-an-experiment"></a>运行试验

若要从笔记本运行试验，请先连接到正在运行的[计算实例](concept-compute-instance.md)。 如果没有计算实例，请使用以下步骤创建一个计算实例： 

1. 选择笔记本工具栏中的“+”。 
2. 为计算命名，并在“虚拟机大小”中选择一个大小。 
3. 选择“创建” 。
4. 计算实例会自动连接到笔记本，现在就可以运行单元格了。

只有本人可以查看和使用自己创建的计算实例。  用户文件与 VM 分开存储，并在工作区中的所有计算实例之间共享。

### <a name="view-logs-and-output"></a>查看日志和输出

使用[笔记本小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true)可以查看运行进度和日志。 小组件具有异步性，在训练结束之前，它会一直提供更新。 Jupyter 和 JupterLab 也支持 Azure 机器学习小组件。

## <a name="change-the-notebook-environment"></a>更改笔记本环境

使用笔记本工具栏可以更改笔记本的运行环境。  

以下操作不会更改笔记本状态或笔记本中任何变量的值：

|操作  |结果  |
|---------|---------| --------|
|停止内核     |  停止任何正在运行的单元格。 运行单元格会自动重新启动内核。 |
|导航到另一个工作区部分     |     停止运行单元格。 |

以下操作将会重置笔记本状态，并重置笔记本中的所有变量。

|操作  |结果  |
|---------|---------| --------|
| 更改内核 | 笔记本使用新内核 |
| 切换计算    |     笔记本自动使用新的计算。 |
| 重置计算 | 在你尝试运行单元格时再次启动 |
| 停止计算     |    不会运行任何单元格  |
| 在 Jupyter 或 JupyterLab 中打开笔记本     |    笔记本在新选项卡中打开。  |

### <a name="add-new-kernels"></a>添加新内核

笔记本会自动查找连接的计算实例上安装的所有 Jupyter 内核。  若要向计算实例添加内核，请执行以下步骤：

1. 选择笔记本工具栏中的“[打开终端](#terminal)”。
1. 使用终端窗口创建新环境。  例如，以下代码会创建 `newenv`：
    ```shell
    conda create --name newenv
    ```
1. 激活该环境。  例如，创建 `newenv` 的结果如下：

    ```shell
    conda activate newenv
    ```
1. 在新环境中安装 pip 和 ipykernel 包，并为该 conda 环境创建内核

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

可以安装任何[可用的 Jupyter 内核](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)。

### <a name="status-indicators"></a>状态指示器

“计算”下拉列表旁的指示器显示计算的状态。  在计算的下拉列表中也会显示状态。  

|Color |计算状态 |
|---------|---------| 
| 绿色 | 正在运行计算 |
| Red |计算失败 | 
| 黑色 | 已停止计算 |
|  浅蓝色 |正在创建、正在启动、正在重新启动、正在设置计算 |
|  灰色 |正在删除、正在停止计算 |

“内核”下拉列表旁的指示器显示内核的状态。

|Color |内核状态 |
|---------|---------|
|  绿色 |内核已连接、空闲、繁忙|
|  灰色 |内核未连接 |

## <a name="find-compute-details"></a>查找计算详细信息 

有关计算实例的详细信息，可以访问[工作室](https://ml.azure.com)的“计算”页。

## <a name="next-steps"></a>后续步骤

* [运行第一个试验](tutorial-1st-experiment-sdk-train.md)
* [使用快照备份文件存储](../storage/files/storage-snapshots-files.md)
