---
title: 设置开发环境 |Python
titleSuffix: Azure Machine Learning
description: 了解如何为 Azure 机器学习设置 Python 开发环境。 使用 Conda 环境、创建配置文件，以及配置自己的基于云的 Notebook 服务器、Jupyter Notebook、Azure Databricks、IDE、代码编辑器和 Data Science Virtual Machine。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 54c607ebac02a9d7e534d24656a8687e9ff39725
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533173"
---
# <a name="set-up-a-development-environment-for-azure-machine-learning"></a>为 Azure 机器学习设置开发环境

了解如何为 Azure 机器学习配置 Python 开发环境。

下表描述了本文所述的每个开发环境及其优点和缺点。

| 环境 | 优点 | 缺点 |
| --- | --- | --- |
| [本地环境](#local) | 可以全面控制开发环境和依赖项。 使用所选的任何生成工具、环境或 IDE 来运行。 | 入门需要更长的时间。 必须安装必要的 SDK 包，此外，必须安装一个环境（如果尚未安装）。 |
| [Azure 机器学习计算实例](#compute-instance) | 最容易入门。 整个 SDK 已安装在工作区 VM 中，笔记本教程已预先克隆，随时可供运行。 | 缺少对开发环境和依赖项的控制。 Linux VM 会产生额外的成本（可以停止不使用的 VM，以免产生费用）。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)。 |
| [Azure Databricks](#aml-databricks) | 非常适合用于在可缩放的 Apache Spark 平台上运行大规模的密集型机器学习工作流。 | 对于试验性机器学习或较小规模的试验和工作流而言性能过剩。 Azure Databricks 会产生额外的成本。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/databricks/)。 |
| [Data Science Virtual Machine (DSVM)](#dsvm) | 类似于基于云的计算实例（已预装 Python 和 SDK），但预装了其他流行的数据科学和机器学习工具。 易于缩放，并可与其他自定义工具和工作流结合使用。 | 与基于云的计算实例相比，入门过程更慢。 |

本文还将提供以下工具的更多用法提示：

* Jupyter Notebook：如果已在使用 Jupyter Notebook，则应安装 SDK 的某些附加功能。

* Visual Studio Code：如果你使用 Visual Studio Code，则 [Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) 包含对 Python 的广泛语言支持以及用于使使用 Azure 机器学习更方便、更有效的功能。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 如果没有，可以通过 [Azure 门户](how-to-manage-workspace.md)、 [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)和 [Azure 资源管理器模板](how-to-create-workspace-template.md)创建 Azure 机器学习工作区。

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> (Local and DSVM 仅) 创建工作区配置文件

工作区配置文件是一个 JSON 文件，用于告知 SDK 如何与 Azure 机器学习工作区进行通信。 该文件命名为 *config.json*，其格式如下：

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

此 JSON 文件必须采用包含 Python 脚本或 Jupyter Notebook 的目录结构。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。

若要在代码中使用此文件，请使用 [`Workspace.from_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#from-config-path-none--auth-none---logger-none---file-name-none-&preserve-view=true) 方法。 此代码从文件中加载信息，并连接到工作区。

使用以下方法之一创建工作区配置文件：

* Azure 门户

    **下载文件**：在[Azure 门户](https://ms.portal.azure.com)中，选择工作区的“概览”部分中的“**下载 config.json”** 。

    ![Azure 门户](./media/how-to-configure-environment/configure.png)

* Azure 机器学习 Python SDK

    创建一个脚本，用于连接到 Azure 机器学习工作区，并使用 [`write_config`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#write-config-path-none--file-name-none-&preserve-view=true) 方法生成文件并将其另存为 *azureml/config.js*。 请确保将 `subscription_id` 、和替换为 `resource_group` 自己的 `workspace_name` 。

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

## <a name="local-computer"></a><a id="local"></a>本地计算机

若要配置本地开发环境 (这可能也是远程虚拟机，如 Azure 机器学习计算实例或 DSVM) ：

1.  (virtualenv，conda) 创建 Python 虚拟环境。

    > [!NOTE]
    > 尽管不是必需的，但建议使用 [Anaconda](https://www.anaconda.com/download/) 或 [Miniconda) ](https://www.anaconda.com/download/) 来管理 Python 虚拟环境和安装包。

    > [!IMPORTANT]
    > 如果在 Linux 或 macOS 上操作，并使用除 bash 以外的 shell（例如 zsh），则在运行某些命令时可能会收到错误消息。 若要解决此问题，请使用 `bash` 命令启动新的 bash shell，然后运行命令。

1. 激活新创建的 Python 虚拟环境。
1. 安装 [Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
1. 若要将本地环境配置为使用 Azure 机器学习工作区，请 [创建一个工作区配置文件或使用现有的配置文件](#workspace) 。

设置本地环境后，便可以开始使用 Azure 机器学习。 请参阅 [Azure 机器学习 Python 入门指南](tutorial-1st-experiment-sdk-setup-local.md) ，开始着手。

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter Notebook

在运行本地 Jupyter Notebook 服务器时，建议为 Python 虚拟环境创建 IPython 内核。 这有助于确保预期内核和包导入行为。

1. 启用环境特定的 IPython 内核

    ```bash
    conda install notebook ipykernel
    ```

1. 为 Python 虚拟环境创建内核。 请确保将替换为 `<myenv>` Python 虚拟环境的名称。

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. 启动 Jupyter Notebook 服务器

请参阅 [Azure 机器学习笔记本存储库](https://github.com/Azure/MachineLearningNotebooks) ，开始处理 Azure 机器学习和 Jupyter 笔记本。

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

若要使用 Visual Studio Code 进行开发：

1. 安装 [Visual Studio Code](https://code.visualstudio.com/Download)。
1.  (预览) 安装 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md) 。

安装 Visual Studio Code 扩展后，可以管理 [Azure 机器学习资源](how-to-manage-resources-vscode.md)、 [运行和调试试验](how-to-debug-visual-studio-code.md)，以及 [部署定型模型](tutorial-train-deploy-image-classification-model-vscode.md)。

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure 机器学习计算实例

Azure 机器学习 [计算实例](concept-compute-instance.md) 是一种基于云的安全 Azure 工作站，它向数据科学家提供 Jupyter Notebook 服务器、JupyterLab 和完全托管的机器学习环境。

无需为计算实例安装或配置任何组件。  

随时可从 Azure 机器学习工作区内部创建组件。 只需提供名称并指定 Azure VM 类型即可。 请参考以下文章尝试操作计算实例：[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)。

若要了解计算实例的详细信息（包括如何安装包），请参阅[计算实例](concept-compute-instance.md)。

> [!TIP]
> 若要防止对未使用的计算实例产生费用，请 [停止计算实例](tutorial-1st-experiment-bring-data.md#clean-up-resources)。

除了 Jupyter Notebook server 和 JupyterLab，还可以在 [Azure 机器学习 studio 内的集成笔记本功能](how-to-run-jupyter-notebooks.md)中使用计算实例。

你还可以使用 Azure 机器学习 Visual Studio Code 扩展将 [Azure 机器学习计算实例配置为远程 Jupyter Notebook 服务器](how-to-set-up-vs-code-remote.md#configure-compute-instance-as-remote-notebook-server)。

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Data Science Virtual Machine

DSVM 是自定义的虚拟机 (VM) 映像。 它是为数据科学工作设计的，它是预配置的工具和软件，如下所示：

  - TensorFlow、PyTorch、Scikit-learn、XGBoost 和 Azure 机器学习 SDK 等包
  - Spark Standalone 和 Drill 等常用数据科学工具
  - Azure CLI、AzCopy 和存储资源管理器等 Azure 工具
  - Visual Studio Code 和 PyCharm 等集成开发环境 (IDE)
  - Jupyter Notebook 服务器

有关更全面的工具列表，请参阅 [DSVM 包含的工具指南](data-science-virtual-machine/tools-included.md)。

> [!IMPORTANT]
> 如果计划使用 DSVM 作为定型或推断作业的 [计算目标](concept-compute-target.md) ，则仅支持 Ubuntu。

将 DSVM 用作开发环境

1. 使用以下方法之一创建 DSVM：

    * 使用 Azure 门户创建 [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) 或 [Windows](data-science-virtual-machine/provision-vm.md) DSVM。
    * [使用 ARM 模板创建 DSVM](data-science-virtual-machine/dsvm-tutorial-resource-manager.md)。
    * 使用 Azure CLI

        若要创建 Ubuntu DSVM，请使用以下命令：

        ```azurecli-interactive
        # create a Ubuntu DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        若要创建 Windows DSVM，请使用以下命令：

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. 激活包含 Azure 机器学习 SDK 的 conda 环境。

    * 对于 Ubuntu DSVM：

        ```bash
        conda activate py36
        ```

    * 对于 Windows DSVM：

        ```bash
        conda activate AzureML
        ```

1. 若要将 DSVM 配置为使用 Azure 机器学习工作区，请 [创建一个工作区配置文件](#workspace) 或使用现有的。

与本地环境类似，你可以使用 Visual Studio Code 和 [Azure 机器学习 Visual Studio Code 扩展](#vscode) 与 Azure 机器学习进行交互。

有关详细信息，请参阅 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="azure-databricks"></a><a name="aml-databricks"></a> Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它通过基于 CPU 或 GPU 的计算群集提供基于笔记本的协作环境。

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

## <a name="next-steps"></a>后续步骤

- 在 Azure 机器学习中使用 MNIST 数据集[训练模型](tutorial-train-models-with-aml.md)
- 查看[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 参考文档
