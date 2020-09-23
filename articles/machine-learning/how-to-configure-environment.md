---
title: 设置 Python 开发环境
titleSuffix: Azure Machine Learning
description: 了解如何配置 Azure 机器学习的开发环境。 使用 Conda 环境、创建配置文件，以及配置自己的基于云的 Notebook 服务器、Jupyter Notebook、Azure Databricks、IDE、代码编辑器和 Data Science Virtual Machine。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4ccf89a4dcb2c91cfdd96b20d74b7f31596b6249
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898277"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>配置 Azure 机器学习的开发环境


本文介绍如何将开发环境配置为使用 Azure 机器学习。 Azure 机器学习不区分平台。 开发环境的唯一要求是提供 Python 3。 另外建议提供一个隔离的环境，例如 Anaconda 或 Virtualenv。

下表描述了本文所述的每个开发环境及其优点和缺点。

| 环境 | 优点 | 缺点 |
| --- | --- | --- |
| [基于云的 Azure 机器学习计算实例](#compute-instance) | 最容易入门。 整个 SDK 已安装在工作区 VM 中，笔记本教程已预先克隆，随时可供运行。 | 缺少对开发环境和依赖项的控制。 Linux VM 会产生额外的成本（可以停止不使用的 VM，以免产生费用）。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 |
| [本地环境](#local) | 可以全面控制开发环境和依赖项。 使用所选的任何生成工具、环境或 IDE 来运行。 | 入门需要更长的时间。 必须安装必要的 SDK 包，此外，必须安装一个环境（如果尚未安装）。 |
| [Azure Databricks](#aml-databricks) | 非常适合用于在可缩放的 Apache Spark 平台上运行大规模的密集型机器学习工作流。 | 对于试验性机器学习或较小规模的试验和工作流而言性能过剩。 Azure Databricks 会产生额外的成本。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/databricks/)。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | 类似于基于云的计算实例（已预装 Python 和 SDK），但预装了其他流行的数据科学和机器学习工具。 易于缩放，并可与其他自定义工具和工作流结合使用。 | 与基于云的计算实例相比，入门过程更慢。 |

本文还将提供以下工具的更多用法提示：

* [Jupyter Notebook](#jupyter)：如果你已在使用 Jupyter Notebook，SDK 中包含了需要安装的某些附加功能。

* [Visual Studio Code](#vscode)：如果使用 Visual Studio Code，[Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)包含对 Python 的广泛语言支持，以及更方便、更高效地使用 Azure 机器学习的功能。

## <a name="prerequisites"></a>先决条件

Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。 只需创建一个工作区，即可开始使用自己的[基于云的 Notebook 服务器](#compute-instance)、[DSVM](#dsvm) 或 [Azure Databricks](#aml-databricks)

若要为[本地计算机](#local)、[Jupyter Notebook 服务器](#jupyter)或 [Visual Studio Code](#vscode) 安装 SDK 环境，还需要：

- [Anaconda](https://www.anaconda.com/download/) 或 [Miniconda](https://conda.io/miniconda.html) 包管理器。

- 在 Linux 或 macOS 上，需要 bash shell。

    > [!TIP]
    > 如果在 Linux 或 macOS 上操作，并使用除 bash 以外的 shell（例如 zsh），则在运行某些命令时可能会收到错误消息。 若要解决此问题，请使用 `bash` 命令启动新的 bash shell，然后运行命令。

- 在 Windows 上，需要命令提示符或 Anaconda 提示符（由 Anaconda 和 Miniconda 安装）。

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>自己的基于云的计算实例

Azure 机器学习 [计算实例](concept-compute-instance.md) 是一种基于云的安全 Azure 工作站，它向数据科学家提供 Jupyter 笔记本服务器、JupyterLab 和完全准备好的 ML 环境。

无需为计算实例安装或配置任何组件。  随时可从 Azure 机器学习工作区内部创建组件。 只需提供名称并指定 Azure VM 类型即可。 请参考以下文章尝试操作计算实例：[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)。

若要了解计算实例的详细信息（包括如何安装包），请参阅[计算实例](concept-compute-instance.md)。

若要避免产生计算费用，请[停止计算实例](tutorial-1st-experiment-bring-data.md#clean-up-resources)。

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

DSVM 是自定义的虚拟机 (VM) 映像。 它专为数据科学工作而设计，其中预配置了：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 和 Azure 机器学习 SDK 等包
  - Spark Standalone 和 Drill 等常用数据科学工具
  - Azure CLI、AzCopy 和存储资源管理器等 Azure 工具
  - Visual Studio Code 和 PyCharm 等集成开发环境 (IDE)
  - Jupyter Notebook 服务器

Azure 机器学习 SDK 适用于 Ubuntu 或 Windows 版本的 DSVM。 但是，如果还计划将 DSVM 用作计算目标，则仅支持 Ubuntu。

若要使用 DSVM 作为开发环境：

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

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * 若要创建 Windows Data Science Virtual Machine，请使用以下命令：

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. DSVM 上已安装 Azure 机器学习 SDK。 若要使用包含此 SDK 的 Conda 环境，请使用以下某个命令：

    * 对于 Ubuntu DSVM：

        ```bash
        conda activate py36
        ```

    * 对于 Windows DSVM：

        ```bash
        conda activate AzureML
        ```

1. 若要验证是否可以访问 SDK 并检查版本，请使用以下 Python 代码：

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. 若要将 DSVM 配置为使用你的 Azure 机器学习工作区，请参阅[创建工作区配置文件](#workspace)部分。

有关详细信息，请参阅 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="local-computer"></a><a id="local"></a>本地计算机

使用本地计算机（也可能是远程虚拟机）时，请创建 Anaconda 环境并安装 SDK。 下面是一个示例：

1. 如果尚未安装 [Anaconda](https://www.anaconda.com/distribution/#download-section)（Python 3.7 版），请下载并安装。

1. 打开 Anaconda 提示符，使用以下命令创建环境：

    运行以下命令来创建环境。

    ```bash
    conda create -n myenv python=3.7.7
    ```

    然后激活该环境。

    ```bash
    conda activate myenv
    ```

    此示例使用 Python 3.7.7 创建环境，但可以选择任何特定的子版本。 使用某些主要版本（建议使用 3.5+）时，不一定能够保证 SDK 的兼容性。如果遇到错误，我们建议在 Anaconda 环境中尝试不同的版本/子版本。 下载组件和包时，创建环境需要花费几分钟。

1. 在新环境中运行以下命令，启用特定于环境的 I Python 内核。 这可以确保在 Anaconda 环境中使用 Jupyter 笔记本时，内核和包导入的行为符合预期：

    ```bash
    conda install notebook ipykernel
    ```

    然后运行以下命令来创建内核：

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. 使用以下命令安装包：

    此命令安装包含笔记本和 `automl` 附加项的基础 Azure 机器学习 SDK。 `automl` 附加项是一个较大的安装包，如果你不打算运行自动化机器学习试验，可以从方括号中删除此附加项。 默认情况下，`automl` 附加项还包含 Azure 机器学习数据准备 SDK 作为依赖项。

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * 如果有消息指出无法卸载 PyYAML，请改用以下命令：
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * 从 macOS Catalina 开始，zsh (Z shell) 是默认的登录 shell 和交互式 shell。 在 zsh 中，运行以下命令以使用“\\”（反斜杠）来转义方括号：
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   安装 SDK 可能需要几分钟时间。 有关安装选项的详细信息，请参阅[安装指南](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。

1. 安装机器学习试验的其他包。

    使用以下任一命令（请将 *\<new package>* 替换为要安装的包）。 通过 `conda install` 安装包时，该包必须是当前通道的一部分（可在 Anaconda 云中添加新通道）。

    ```bash
    conda install <new package>
    ```

    或者，可以通过 `pip` 安装包。

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook 是 [Jupyter 项目](https://jupyter.org/)的一部分。 它们提供交互式编码体验，可用于创建将实时代码与叙述性文本和图形混合在一起的文档。 Jupyter Notebook 也是与他人共享结果的好方法，因为可用于将代码部分的输出保存在文档中。 可以在各种平台上安装 Jupyter Notebook。

[本地计算机](#local)部分所述的过程将会安装在 Anaconda 环境中运行 Jupyter Notebook 所需的组件。

若要在 Jupyter Notebook 环境中启用这些组件：

1. 打开 Anaconda 提示符并激活环境。

    ```bash
    conda activate myenv
    ```

1. 克隆一组示例笔记本的 [GitHub 存储库](https://github.com/Azure/MachineLearningNotebooks)。

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用以下命令启动 Jupyter Notebook 服务器：

    ```bash
    jupyter notebook
    ```

1. 若要验证 Jupyter Notebook 是否可以使用 SDK，请创建一个**新的**笔记本，选择“Python 3”作为内核，然后在笔记本单元中运行以下命令：

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 如果在导入模块时遇到问题并收到 `ModuleNotFoundError`，请在笔记本单元中运行以下代码，确保 Jupyter 内核连接到环境的正确路径。

    ```python
    import sys
    sys.path
    ```

1. 若要将 Jupyter Notebook 配置为使用你的 Azure 机器学习工作区，请转到[创建工作区配置文件](#workspace)部分。

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code 是一款非常流行的跨平台代码编辑器，它通过 [Visual Studio 市场](https://marketplace.visualstudio.com/vscode)中提供的扩展支持广泛的编程语言和工具。 [Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)将安装 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)，用于在所有类型的 Python 环境（虚拟环境、Anaconda 等）中编写代码。 此外，它还提供便利的功能用于处理 Azure 机器学习资源和运行 Azure 机器学习试验，而无需退出 Visual Studio Code。

若要使用 Visual Studio Code 进行开发：

1. 若要安装适用于 Visual Studio Code 的 Azure 机器学习扩展，请参阅 [Azure 机器学习](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)。

    有关详细信息，请参阅[使用适用于 Visual Studio Code 的 Azure 机器学习](tutorial-setup-vscode-extension.md)。

1. 若要了解如何使用 Visual Studio Code 进行任何类型的 Python 开发，请参阅 [VSCode 中的 Python 入门](https://code.visualstudio.com/docs/python/python-tutorial)。

    - 若要选择包含 SDK 的 SDK Python 环境，请打开 VS Code，然后按 Ctrl+Shift+P（Linux 和 Windows）或 Command+Shift+P (Mac)。
        - 此时会打开“命令面板”。

    - 输入 Python:__Select Interpreter__，然后选择相应的环境

1. 若要验证是否可以使用 SDK，请创建包含以下代码的新 Python 文件 (.py)：

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    单击“运行单元”CodeLens 或直接按 Shift+Enter 运行此代码。
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它提供基于 Notebook 的协作环境，其中包含基于 CPU 或 GPU 的计算群集。

Azure Databricks 如何使用 Azure 机器学习：
+ 可以使用 Spark MLlib 训练模型，然后在 Azure Databricks 内部将该模型部署到 ACI/AKS。
+ 也可以在包含 Azure Databricks 的特殊 Azure ML SDK 中使用[自动化机器学习](concept-automated-ml.md)功能。
+ 可以从 [Azure 机器学习管道](concept-ml-pipelines.md)将 Azure Databricks 用作计算目标。

### <a name="set-up-your-databricks-cluster"></a>设置 Databricks 群集

创建 [Databricks 群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 仅当在 Databricks 上安装适用于自动化机器学习的 SDK 时，某些设置才适用。
**创建群集需要几分钟时间。**

使用以下设置：

| 设置 |适用于| Value |
|----|---|---|
| 群集名称 |通用| yourclustername |
| Databricks Runtime |通用|非 ML 运行时 6.5（scala 2.11、spark 2.4.3） |
| Python 版本 |通用| 3 |
| 工作节点 |通用| 2 个或以上 |
| 工作节点 VM 类型 <br>（确定最大并发迭代数） |自动化机器学习<br>（仅限）| 首选内存优化的 VM |
| 启用自动缩放 |自动化机器学习<br>（仅限）| 取消选中 |

请等待群集运行完成，然后继续操作。

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>将正确的 SDK 安装到 Databricks 库中
群集运行后，请[创建一个库](https://docs.databricks.com/user-guide/libraries.html#create-a-library)用于将相应的 Azure 机器学习 SDK 包附加到群集。

1. 右键单击用于存储该库的当前工作区文件夹。 选择“创建” > “库”。 

1. 请仅选择**一个**选项（不支持其他 SDK 安装）

   |SDK 包附加项|Source|PyPi 名称|
   |----|---|---|
   |对于 Databricks| 上传 Python Egg 或 PyPI | azureml-sdk[databricks]|
   |对于包含自动化<br> ML 功能的 Databricks| 上传 Python Egg 或 PyPI | `azureml-sdk[automl]`|

   > [!Warning]
   > 无法安装其他 SDK 附加项。 请仅选择上述选项中的一个，即 [`databricks`] 或 [`automl`]。

   * 不要选择“自动附加到所有群集”。
   * 选择群集名称旁边的“附加”。

1. 监视错误，直到状态更改为“已附加”。这可能需要几分钟时间。  如果此步骤失败：

   请尝试通过以下操作重启群集：
   1. 在左窗格中，选择“群集”。
   1. 在表中选择你的群集名称。
   1. 在“库”选项卡上，选择“重启”。 

   此外，应考虑到：
   + 在 AutoML 配置中使用 Azure Databricks 时添加以下参数：
       1. ```max_concurrent_iterations``` 基于群集中的工作器节点数。
        2. ```spark_context=sc``` 基于默认的 Spark 上下文。
   + 或者，如果使用的是旧版 SDK，请从群集的已安装库中取消选择该版本，并将其移到垃圾桶中。 安装新的 SDK 版本并重启群集。 如果重启后出现问题，请分离并重新附加群集。

如果安装成功，则导入的库应如下所示：

**不包含**自动化机器学习的 Databricks 的 SDK ![适用于 Databricks 的 Azure 机器学习 SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

**包含**自动化机器学习的 Databricks 的 SDK ![Databricks 上安装的包含自动化机器学习的 SDK](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>开始探索

尝试以下操作：
+ 尽管有许多示例笔记本可用，但**只有[这些示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks)适用于 Azure Databricks。**

+ 直接从工作区导入这些示例。 请参见下图：![选择“导入”](./media/how-to-configure-environment/azure-db-screenshot.png)
![导入面板](./media/how-to-configure-environment/azure-db-import.png)

+ 了解如何[创建包含 Databricks 的管道用作训练计算](how-to-create-your-first-pipeline.md)。

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>创建工作区配置文件

工作区配置文件是一个 JSON 文件，用于告知 SDK 如何与 Azure 机器学习工作区进行通信。 该文件命名为 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

此 JSON 文件必须采用包含 Python 脚本或 Jupyter Notebook 的目录结构。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。

要从代码使用此文件，请使用 `ws=Workspace.from_config()`。 此代码从文件中加载信息，并连接到工作区。

可通过三种方式创建配置文件：

* **使用 [ws. write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)** ：编写 *config.json* 文件。 该文件含包含工作区的配置信息。 可以下载 *config.json* 或将其复制到其他开发环境。

* **下载文件**：在[Azure 门户](https://ms.portal.azure.com)中，选择工作区的“概览”部分中的“**下载 config.json”** 。

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

    此代码将配置文件写入 *.azureml/config.json* 文件。

## <a name="next-steps"></a>后续步骤

- 在 Azure 机器学习中使用 MNIST 数据集[训练模型](tutorial-train-models-with-aml.md)
- 查看[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 参考文档
