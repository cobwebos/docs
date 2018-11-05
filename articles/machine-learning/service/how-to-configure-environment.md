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
ms.date: 10/24/2018
ms.openlocfilehash: 6c2d5a776f603161ef730028168b91844c120aec
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158986"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>配置 Azure 机器学习的开发环境

本文介绍如何将开发环境配置为使用 Azure 机器学习服务，具体内容包括：

- 如何创建用于将环境与 Azure 机器学习工作区相关联的配置文件。
- 如何配置以下开发环境：
  - 计算机上的 Jupyter Notebook
  - Visual Studio Code
  - 自定义代码编辑器
- 如何设置 [conda 虚拟环境](https://conda.io/docs/user-guide/tasks/manage-environments.html)并将它用于 Azure 机器学习。 我们建议使用 Continuum Anaconda 来隔离工作环境，以避免包之间发生依赖项冲突。

## <a name="prerequisites"></a>先决条件

- 设置 Azure 机器学习服务工作区。 请遵循 [Azure 机器学习服务入门](quickstart-get-started.md)中的步骤。
- 安装 [Continuum Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 包管理器。
- 如果使用 Visual Studio Code，请获取 [Python 扩展](https://code.visualstudio.com/docs/python/python-tutorial)。

> [!NOTE]
> 可以使用 bash（在 Linux 和 Mac OS 中）或命令提示符（在 Windows 中）测试本文中所示的 shell 命令。

## <a name="create-a-workspace-configuration-file"></a>创建工作区配置文件

Azure 机器学习 SDK 使用工作区配置文件来与 Azure 机器学习服务工作区通信。

- 若要创建该配置文件，请完成 [Azure 机器学习快速入门](quickstart-get-started.md)。
  - 快速入门过程将在 Azure Notebooks 中创建一个 `config.json` 文件。 此文件含包含工作区的配置信息。
  - 将 `config.json` 下载或复制到引用它的脚本或 Notebook 所在的同一目录中。

- 或者，可以使用以下步骤手动生成该文件：

    1. 在 [Azure 门户](https://portal.azure.com)中打开你的工作区。 复制“工作区名称”、“资源组”和“订阅 ID”。 这些值用于创建配置文件。
        ![Azure 门户](./media/how-to-configure-environment/configure.png)

    1. 使用以下 Python 代码创建该文件，并确保在引用工作区的脚本或 Notebook 所在的同一目录中运行该代码：

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        代码将写入以下 `aml_config/config.json` 文件：

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        可以将 `aml_config` 目录或仅仅 `config.json` 文件复制到引用工作区的任何其他目录中。

       > [!NOTE]
       > 同一目录或其子目录中的其他脚本或 Notebook 将使用 `ws=Workspace.from_config()` 加载工作区。

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Azure Notebooks 和 Data Science Virtual Machine

Azure Notebooks 和 Azure Data Science Virtual Machine (DSVM) 配置为使用 Azure 机器学习服务。 这些环境包含所需的组件，例如 Azure 机器学习 SDK。

- Azure Notebooks 是 Azure 云中的 Jupyter Notebook 服务。
- Data Science Virtual Machine 是用于开展数据科学工作的自定义虚拟机 (VM) 映像。 其中包括：
  - 常用工具
  - 集成开发环境 (IDE)
  - Jupyter Notebook、PyCharm 和 Tensorflow 等包
- 仍然需要工作区配置文件才能使用这些环境。

有关将 Azure Notebooks 与 Azure 机器学习服务配合使用的示例，请参阅 [Azure 机器学习服务入门](quickstart-get-started.md)。

有关 Data Science Virtual Machine 的详细信息，请参阅 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="configure-jupyter-notebooks-on-your-computer"></a>在自己的计算机上配置 Jupyter Notebook

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

   可以在以下 SDK 中查看类和方法的 Python 参考文档：
   + [适用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk)
   + [Azure 机器学习数据准备 SDK](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > 如果有消息指出无法卸载 `PyYAML`，请改用以下命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   安装 SDK 可能需要几分钟时间。

1. 安装机器学习试验的包。 使用以下命令（请将 `<new package>` 替换为要安装的包）：

    ```shell
    conda install <new package>
    ```

1. 安装可感知 conda 的 Jupyter Notebook 服务器，并启用试验小组件（用于查看运行信息）。 使用以下命令：

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. 使用以下命令打开 Jupyter Notebook：

    ```shell
    jupyter notebook
    ```

1. 打开新的 Notebook，选择“myenv”作为内核，然后验证是否已安装 Azure 机器学习 SDK。 在 Notebook 单元中运行以下命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>配置 Visual Studio Code

1. 打开命令提示符或 shell。

1. 使用以下命令创建 conda 环境：

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. 使用以下命令安装 Azure 机器学习 SDK 和数据准备 SDK：

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. 安装 Visual Studio Code Tools for AI 扩展。 请参阅 [Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

1. 安装机器学习试验的包。 使用以下命令（请将 `<new package>` 替换为要安装的包）：

    ```shell
    conda install <new package>
    ```

1. 打开 Visual Studio Code，然后使用 **CTRL-SHIFT-P**（在 Windows 中）或 **COMMAND-SHIFT-P**（在 Mac OS 中）打开**命令面板**。 输入 _Python: Select Interpreter_，然后选择创建的 conda 环境。

   > [!NOTE]
   > Visual Studio Code 能够自动感知你的计算机上的 conda 环境。 有关详细信息，请参阅 [Visual Studio 代码文档](https://code.visualstudio.com/docs/python/environments#_conda-environments)。

1. 使用 Visual Studio Code 创建包含以下代码的新 Python 脚本文件，然后运行该文件，以验证配置：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>配置自定义代码编辑器

可对 Azure 机器学习 SDK 使用所选的代码编辑器。

1. 根据前面的[配置 Visual Studio Code](#configure-visual-studio-code) 的步骤 2 中所述创建 conda 环境。
1. 遵照每个编辑器的说明使用 conda 环境。 例如，可以遵照 [PyCharm 说明](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html)。

## <a name="next-steps"></a>后续步骤

- [使用 MNIST 数据集对 Azure 机器学习的模型进行定型](tutorial-train-models-with-aml.md)
