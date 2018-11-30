---
title: 配置 Azure 机器学习的开发环境 | Microsoft 文档
description: 了解在使用 Azure 机器学习服务时如何配置开发环境。 本文档将学习如何使用 Conda 环境、创建配置文件和配置 Jupyter Notebooks、Azure Notebooks、IDE、代码编辑器和数据科学虚拟机。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: fa70e0dfa1f131e38e43faa3d80497d50a52e135
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275208"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>配置 Azure 机器学习的开发环境

本文档将介绍如何使用 Azure 机器学习服务来配置开发环境。 Azure 机器学习服务与平台无关。 开发环境的唯一要求是 Python 3、Conda（用于隔离环境）和包含 Azure 机器学习工作区信息的配置文件。

本文档重点介绍以下特定环境和工具：

* [Azure Notebooks](#aznotebooks)：Azure 云中托管的 Jupyter Notebook 服务。 它是最简单的入门方法，因为已安装 Azure 机器学习 SDK。
* [Data Science Virtual Machine](#dsvm)：Azure 云中的虚拟机，可用于开展数据科学工作。 已安装 Python 3、Conda、Jupyter Notebook 和 Azure 机器学习 SDK。 VM 配备了用于开发 ML 解决方案的常用 ML 框架、工具和编辑器。 对于 Azure 平台上的 ML，它可能是最完整的开发环境。
* [Jupyter Notebook](#jupyter)：如果已在使用 Jupyter Notebook，则应安装 SDK 的某些附加功能。
* [Visual Studio Code](#vscode)：如果使用 Visual Studio Code，则可以安装一些实用的扩展。

如果还没有 Python 3 环境，或者只想了解安装 SDK 的基本步骤，请参阅[本地计算机](#local)部分。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习服务工作区。 请按照 [Azure 机器学习服务入门](quickstart-get-started.md)中的步骤创建工作区。

- [Continuum Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 包管理器。

    > [!IMPORTANT]
    > 使用 Azure Notebooks 时，不需要 Anaconda 和 Miniconda。

- 在 Linux 或 Mac OS 上，需要 bash shell。

    > [!TIP]
    > 如果是在 Linux 或 Mac OS 上，并使用除 bash 以外的 shell（例如 zsh），则在运行某些命令时可能会收到错误消息。 若要解决此问题，请使用 `bash` 命令启动新的 bash shell，然后运行命令。

- 在 Windows 上，需要命令提示符或 Anaconda 提示符（由 Anaconda 和 Miniconda 安装）。

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)（预览版）是 Azure 云中的交互式开发环境。 它是 Azure 机器学习开发入门最简单的方法。

* 已安装 Azure 机器学习 SDK。
* 在 Azure 门户中创建了 Azure 机器学习服务工作区之后，可以单击按钮以将 Azure Notebook 环境自动配置为使用工作区。

若要开始使用 Azure Notebooks 进行开发，请按照 [Azure 机器学习服务入门](quickstart-get-started.md)文档进行操作。

## <a id="dsvm"></a>Data Science Virtual Machine

Data Science Virtual Machine (DSVM) 是用于开展数据科学工作的自定义虚拟机 (VM) 映像。 其中包括：

  - 常用数据科学工具
  - 集成开发环境 (IDE)，如 PyCharm 和 RStudio
  - Jupyter Notebook 和 Tensorflow 等包

Azure 机器学习 SDK 适用于 Ubuntu 或 Windows 版本的 DSVM。 若要使用 DSVM 作为开发环境，请按照以下步骤进行操作：

1. 若要创建 Data Science Virtual Machine，请按照以下某个文档中的步骤进行操作：

    * [创建 Ubuntu Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)
    * [创建 Windows Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

1. DSVM 上已安装 Azure 机器学习 SDK。 若要使用包含此 SDK 的 Conda 环境，请使用以下某个命令：

    * 在 Ubuntu DSVM 上，使用此命令：

        ```shell
        conda activate py36
        ```

    * 在 Windows DSVM 上，使用此命令：

        ```shell
        conda activate AzureML
        ```

1. 若要验证是否可以访问 SDK 并检查版本，请使用以下 Python 代码：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要将 DSVM 配置为使用 Azure 机器学习服务工作区，请参阅[配置工作区](#workspace)部分。

有关 Data Science Virtual Machine 的详细信息，请参阅 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a id="local"></a>本地计算机

使用本地计算机（也可能是远程虚拟机）时，请使用以下步骤创建 conda 环境并安装 SDK：

1. 打开命令提示符或 shell。

1. 使用以下命令创建 conda 环境：

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    创建环境可能需要几分钟的时间才能完成，因为需要下载 Python 3.6 和其他组件。

1. 使用以下命令安装 Azure 机器学习 SDK 以及 Notebook 附加组件和数据准备 SDK：

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > 如果有消息指出无法卸载 `PyYAML`，请改用以下命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   安装 SDK 可能需要几分钟时间。

1. 安装机器学习试验的包。 使用以下命令（请将 `<new package>` 替换为要安装的包）：

    ```shell
    conda install <new package>
    ```

1. 若要验证是否安装了 SDK，可使用以下 Python 代码：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 项目](https://jupyter.org/)的一部分。 它们提供交互式编码体验，可用于创建将实时代码与叙述性文本和图形混合在一起的文档。 Jupyter Notebook 也是与他人共享结果的好方法，因为可用于将代码部分的输出保存在文档中。 可以在各种平台上安装 Jupyter Notebook。

[本地计算机](#local)部分中的步骤安装了 Jupyter Notebook 的可选组件。 若要在 Jupyter Notebook 环境中启用这些组件，请使用以下步骤：

1. 打开命令提示符或 shell。

1. 若要安装可感知 conda 的 Jupyter Notebook 服务器并启用试验小组件，请使用以下命令：

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.widgets
    ```

1. 使用以下命令打开 Jupyter Notebook：

    ```shell
    jupyter notebook
    ```

1. 若要验证 Jupyter Notebook 是否可以使用 SDK，请打开一个新的笔记本，并选择“myenv”作为内核。 然后，在笔记本单元中运行以下命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要将 Jupyter Notebook 配置为使用 Azure 机器学习服务工作区，请参阅[配置工作区](#workspace)部分。

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code 是跨平台代码编辑器。 它依赖于本地 Python 3 和 Python 支持的 Conda 安装，但它提供用于 AI 的其他工具。 它还支持从代码编辑器中选择 Conda 环境。

若要使用 Visual Studio Code 进行开发，请使用以下步骤：

1. 若要了解如何使用 Visual Studio Code 进行 Python 开发，请参阅 [VSCode 中的 Python 入门](https://code.visualstudio.com/docs/python/python-tutorial)文档。

1. 若要选择 Conda 环境，请打开 VS Code，然后使用 Ctrl-Shift-P（Linux 和 Windows）或 Command-Shift-P (Mac) 获取命令托盘。 输入 Python: Select Interpreter，然后选择 conda 环境。

1. 若要验证是否可以使用 SDK，请创建包含以下代码的新 Python 文件 (.py)。 然后运行该文件：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要安装 Visual Studio Code Tools for AI 扩展，请参阅 [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

    有关详细信息，请参阅[结合 Azure 机器学习使用 VS Code Tools for AI](how-to-vscode-tools.md) 文档。

## <a id="workspace"></a>创建工作区配置文件

工作区配置文件是一个 JSON 文档，用于告知 SDK 如何与 Azure 机器学习服务工作区进行通信。 该文件命名为 `config.json`，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

此文件必须采用包含 Python 脚本或 Jupyter Notebook 的目录结构。 它可以位于同一目录（名为 `aml_config` 的子目录）中，也可以位于父目录中。

要从代码使用此文件，请使用 `ws=Workspace.from_config()`。 此代码从文件中加载信息，并连接到工作区。

创建配置文件有三种方法：

* 如果按照 [Azure 机器学习快速入门](quickstart-get-started.md)进行操作，则可以在 Azure Notebooks 库中创建一个 `config.json` 文件。 此文件含包含工作区的配置信息。 可以下载此 `config.json` 或将其复制到其他开发环境。

* 可使用文本编辑器手动创建文件。 通过访问 [Azure 门户](https://portal.azure.com)中的工作区，可以查找配置文件中的值。 复制工作区名称、资源组和订阅 ID 的值，并在配置文件中使用这些值。
        ![Azure 门户](./media/how-to-configure-environment/configure.png)

* 可采用编程方式创建文件。 以下代码片段演示如何通过提供订阅 ID、资源组和工作区名称来连接到工作区。 然后它将工作区配置保存到文件：

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    此代码将配置文件写入 `aml_config/config.json` 文件。

## <a name="next-steps"></a>后续步骤

- [使用 MNIST 数据集对 Azure 机器学习的模型进行定型](tutorial-train-models-with-aml.md)
- [适用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk)
- [Azure 机器学习数据准备 SDK](https://aka.ms/data-prep-sdk)