---
title: 设置 Python 开发环境
titleSuffix: Azure Machine Learning service
description: 了解在使用 Azure 机器学习服务时如何配置开发环境。 本文介绍如何使用 Conda 环境、创建配置文件，以及配置 Jupyter Notebook、Azure Notebooks、IDE、代码编辑器和 Data Science Virtual Machine。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 46a1872d2ac5d1670620148edf7ee273580826d3
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811267"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>配置 Azure 机器学习的开发环境

本文介绍如何将开发环境配置为使用 Azure 机器学习服务。 机器学习服务不区分平台。 

开发环境的唯一要求是提供 Python 3、Conda（用于隔离环境）和包含 Azure 机器学习工作区信息的配置文件。

本文重点介绍以下环境和工具：

* [Azure Notebooks](#aznotebooks)：Azure 云中托管的 Jupyter Notebook 服务。 这是最简单的入门方法，因为已安装 Azure 机器学习 SDK。

* [Data Science Virtual Machine (DSVM)](#dsvm)：Azure 云中的预配置开发或试验环境，用于开展数据科学工作，可以部署到仅限 CPU 的 VM 实例或基于 GPU 的实例。 已安装 Python 3、Conda、Jupyter Notebook 和 Azure 机器学习 SDK。 VM 配备了用于开发机器学习解决方案的常用机器学习和深度学习框架、工具与编辑器。 对于 Azure 平台上的机器学习，它可能是最完整的开发环境。

* [Jupyter Notebook](#jupyter)：如果你已在使用 Jupyter Notebook，SDK 中包含了需要安装的某些附加功能。

* [Visual Studio Code](#vscode)：如果使用 Visual Studio Code，其中包含了一些可以安装的有用扩展。

* [Azure Databricks](#aml-databricks)：基于 Apache Spark 的常用数据分析平台。 了解如何将 Azure 机器学习 SDK 添加到群集中，以便部署模型。

如果还没有 Python 3 环境，或者只想了解安装 SDK 的基本步骤，请参阅[本地计算机](#local)部分。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习服务工作区。 若要创建工作区，请参阅 [Azure 机器学习服务入门](quickstart-get-started.md)。

- [Continuum Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 包管理器。

    > [!IMPORTANT]
    > 使用 Azure Notebooks 时，不需要 Anaconda 和 Miniconda。

- 在 Linux 或 macOS 上，需要 bash shell。

    > [!TIP]
    > 如果在 Linux 或 macOS 上操作，并使用除 bash 以外的 shell（例如 zsh），则在运行某些命令时可能会收到错误消息。 若要解决此问题，请使用 `bash` 命令启动新的 bash shell，然后运行命令。

- 在 Windows 上，需要命令提示符或 Anaconda 提示符（由 Anaconda 和 Miniconda 安装）。

## <a id="anotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)（预览版）是 Azure 云中的交互式开发环境。 这是 Azure 机器学习开发入门的最简单方法。

* 已安装 Azure 机器学习 SDK。
* 在 Azure 门户中创建了 Azure 机器学习服务工作区之后，可以单击相应的按钮将 Azure Notebook 环境自动配置为使用该工作区。

若要开始使用 Azure Notebooks 进行开发，请参阅 [Azure 机器学习服务入门](quickstart-get-started.md)。

## <a id="dsvm"></a>Data Science Virtual Machine

DSVM 是自定义的虚拟机 (VM) 映像。 它专为数据科学工作而设计，其中预配置了：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 和 Azure 机器学习 SDK 等包。
  - Spark Standalone 和 Drill 等常用数据科学工具。
  - Azure CLI、AzCopy 和存储资源管理器等 Azure 工具。
  - Visual Studio Code、PyCharm 和 RStudio 等集成开发环境 (IDE)。
  - Jupyter Notebook 服务器。 

Azure 机器学习 SDK 适用于 Ubuntu 或 Windows 版本的 DSVM。 若要使用 DSVM 作为开发环境，请执行以下操作：

1. 在以下任一环境中创建 DSVM：

    * Azure 门户：

        * [创建 Ubuntu Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [创建 Windows Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI：

        > [!IMPORTANT]
        > * 使用 Azure CLI 时，必须先使用 `az login` 命令登录到 Azure 订阅。
        >
        > * 在此步骤中使用命令时，必须提供资源组名称、VM名称、用户名和密码。

        * 若要创建 Ubuntu Data Science Virtual Machine，请使用以下命令：

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * 若要创建 Windows Data Science Virtual Machine，请使用以下命令：

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. DSVM 上已安装 Azure 机器学习 SDK。 若要使用包含此 SDK 的 Conda 环境，请使用以下某个命令：

    * 对于 Ubuntu DSVM：

        ```shell
        conda activate py36
        ```

    * 对于 Windows DSVM：

        ```shell
        conda activate AzureML
        ```

1. 若要验证是否可以访问 SDK 并检查版本，请使用以下 Python 代码：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要将 DSVM 配置为使用你的 Azure 机器学习服务工作区，请参阅[创建工作区配置文件](#workspace)部分。

有关详细信息，请参阅 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a id="local"></a>本地计算机

使用本地计算机（也可能是远程虚拟机）时，请执行以下步骤创建 Conda 环境并安装 SDK：

1. 打开命令提示符或 shell。

1. 使用以下命令创建 Conda 环境：

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    创建环境可能需要几分钟的时间才能完成，因为需要下载 Python 3.6 和其他组件。

1. 使用以下命令安装 Azure 机器学习 SDK 以及 Notebook 附加组件和数据准备 SDK：

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > 如果有消息指出无法卸载 PyYAML，请改用以下命令：
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   安装 SDK 可能需要几分钟时间。

1. 安装机器学习试验的包。 使用以下命令（请将 *\<new package>* 替换为要安装的包）：

    ```shell
    conda install <new package>
    ```

1. 若要验证是否安装了 SDK，请使用以下 Python 代码：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 项目](https://jupyter.org/)的一部分。 它们提供交互式编码体验，可用于创建将实时代码与叙述性文本和图形混合在一起的文档。 Jupyter Notebook 也是与他人共享结果的好方法，因为可用于将代码部分的输出保存在文档中。 可以在各种平台上安装 Jupyter Notebook。

[本地计算机](#local)部分中的过程可以安装 Jupyter Notebook 的可选组件。 若要在 Jupyter Notebook 环境中启用这些组件，请执行以下操作：

1. 打开命令提示符或 shell。

1. 若要安装可感知 Conda 的 Jupyter Notebook 服务器，请使用以下命令：

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. 使用以下命令打开 Jupyter Notebook：

    ```shell
    jupyter notebook
    ```

1. 若要验证 Jupyter Notebook 是否可以使用 SDK，请打开一个新的 Notebook，选择“myenv”作为内核，然后在 Notebook 单元中运行以下命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要将 Jupyter Notebook 配置为使用你的 Azure 机器学习服务工作区，请转到[创建工作区配置文件](#workspace)部分。

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code 是跨平台代码编辑器。 它依赖于本地 Python 3 和 Python 支持的 Conda 安装，但它提供用于 AI 的其他工具。 它还支持从代码编辑器中选择 Conda 环境。

若要使用 Visual Studio Code 进行开发，请执行以下操作：

1. 若要了解如何使用 Visual Studio Code 进行 Python 开发，请参阅 [VSCode 中的 Python 入门](https://code.visualstudio.com/docs/python/python-tutorial)。

1. 若要选择 Conda 环境，请打开 VS Code，然后按 Ctrl-Shift-P（Linux 和 Windows）或 Command-Shift-P (Mac)。  
    此时会打开“命令面板”。 

1. 输入 __Python:Select Interpreter__，然后选择 Conda 环境。

1. 若要验证是否可以使用 SDK，请创建并运行包含以下代码的新 Python 文件 (.py)。

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 若要安装适用于 Visual Studio Code 的 Azure 机器学习扩展，请参阅 [AI 工具](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

    有关详细信息，请参阅[使用适用于 Visual Studio Code 的 Azure 机器学习](how-to-vscode-tools.md)。

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

可以使用自定义版本的用于 Azure Databricks 的 Azure 机器学习 SDK 进行端到端自定义机器学习。 或者，可以在 Databricks 中训练模型，然后使用 [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) 部署该模型。

准备 Databricks 群集并获取示例笔记本：

1. 使用 Python 3 创建 Databricks 运行时版本为 4.x（首选高并发性）的 [Databricks 群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 

1. 若要安装适用于 Python 的 Azure 机器学习 SDK `azureml-sdk[databricks]` PyPi 包并将其附加到群集，请[创建一个库](https://docs.databricks.com/user-guide/libraries.html#create-a-library)。  
    完成后，按下图所示附加该库。 请注意这些[常见 Databricks 问题](resource-known-issues.md#databricks)。

   ![Databricks 上安装的 SDK ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   如果此步骤失败，请执行以下操作重启群集：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在左窗格中，选择“群集”。 
   
   b. 在表中选择你的群集名称。 

   c. 在“库”选项卡上，选择“重启”。

1. 下载 [Azure Databricks/Azure 机器学习 SDK Notebook 存档文件](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc)。

   >[!Warning]
   > 多个示例笔记本可与 Azure 机器学习服务一起使用。 只有[这些示例 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) 可与 Azure Databricks 一起使用。
   > 

1.  [将此存档文件导入](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) Databricks 群集，并根据[机器学习 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) 页中所述开始浏览。


## <a id="workspace"></a>创建工作区配置文件

工作区配置文件是一个 JSON 文件，用于告知 SDK 如何与 Azure 机器学习服务工作区进行通信。 该文件命名为 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

此 JSON 文件必须采用包含 Python 脚本或 Jupyter Notebook 的目录结构。 它可以位于同一目录（名为 *aml_config* 的子目录）中，也可以位于父目录中。

要从代码使用此文件，请使用 `ws=Workspace.from_config()`。 此代码从文件中加载信息，并连接到工作区。

可通过三种方式创建配置文件：

* **遵循 [Azure 机器学习快速入门](quickstart-get-started.md)**：将在 Azure Notebooks 库中创建一个 *config.json* 文件。 该文件含包含工作区的配置信息。 可以下载 *config.json* 或将其复制到其他开发环境。

* **手动创建文件**：此方法使用文本编辑器。 可以通过访问 [Azure 门户](https://portal.azure.com)中的工作区来查找要在配置文件中提供的值。 复制工作区名称、资源组和订阅 ID 值，并在配置文件中使用这些值。

     ![Azure 门户](./media/how-to-configure-environment/configure.png)

* **以编程方式创建文件**：以下代码片段通过提供订阅 ID、资源组和工作区名称连接到工作区。 然后，它将工作区配置保存到文件中：

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

    此代码将配置文件写入 *aml_config/config.json* 文件。

## <a name="next-steps"></a>后续步骤

- [使用 MNIST 数据集对 Azure 机器学习的模型进行定型](tutorial-train-models-with-aml.md)
- [适用于 Python 的 Azure 机器学习 SDK](https://aka.ms/aml-sdk)
- [Azure 机器学习数据准备 SDK](https://aka.ms/data-prep-sdk)
