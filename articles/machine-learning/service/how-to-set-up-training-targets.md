---
title: 使用 Azure 机器学习服务设置用于定型模型的计算目标 | Microsoft Docs
description: 了解如何选择和配置用于定型机器学习模型的定型环境（计算目标）。 借助 Azure 机器学习服务，可轻松切换定型环境。 请从本地定型入手，如果需要横向扩展，请切换到基于云的计算目标。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 2c4255b70ae9eb3b31b6fdfce33853f0d517aa1f
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215474"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>选择并使用用于定型模型的计算目标

使用 Azure 机器学习服务，可以在不同的环境中训练模型。 这些环境称为“计算目标”，可以位于本地，也可以位于云中。 本文档介绍支持的计算目标及其用法。

计算目标是一种资源，用于运行训练脚本，或托管已部署为 Web 服务的模型。 可使用 Azure 机器学习 SDK 或 CLI 创建和管理计算目标。 若有其他进程（例如，Azure 门户或 Azure CLI）创建的计算目标，使用方法为将这些计算目标附加到 Azure 机器学习服务工作区。

可以先从计算机上的本地运行任务入手，再纵向和横向扩展到其他环境，如使用 GPU 的远程 Data Science Virtual Machine 或 Azure Batch AI。 

>[!NOTE]
> 本文中的代码已使用 Azure 机器学习 SDK 版本 0.168 进行测试 

## <a name="supported-compute-targets"></a>受支持的计算目标

Azure 机器学习服务支持以下计算目标：

|计算目标| GPU 加速 | 自动超参数优化 | 自动模型选择 | 可以在管道中使用|
|----|:----:|:----:|:----:|:----:|
|[本地计算机](#local)| 可能 | &nbsp; | ✓ | &nbsp; |
|[Data Science Virtual Machine (DSVM)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Batch AI](#batch)| ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>* Azure Databricks 和 Azure Data Lake Analytics 只能在管道中使用。 有关管道的详细信息，请参阅 [Azure 机器学习中的管道](concept-ml-pipelines.md)文档。

[Azure 容器实例 (ACI)](#aci) 也可用于定型模型。 它是一款无服务器的云产品/服务，具有价格低廉、易于创建和使用的优点。 不过，ACI 不支持 GPU 加速、自动超参数优化或自动模型选择。 另外，它也不能用于管道中。

各计算目标的关键区别如下：
* __GPU 加速__：GPU 适用于 Data Science Virtual Machine 和 Azure Batch AI。 可以在本地计算机上使用 GPU，具体视已安装的硬件、驱动程序和框架而定。
* __自动超参数优化__：Azure 机器学习自动超参数优化有助于发现最适合模型的参数。
* __自动模型选择__：Azure 机器学习服务可以在生成模型时智能地推荐算法和超参数选择。 相较于手动尝试不同组合，自动模型选择有助于更快地收敛至优质模型。 有关详细信息，请参阅[教程：使用 Azure 自动机器学习自动定型分类模型](tutorial-auto-train-models.md)一文。
* __管道__：借助 Azure 机器学习服务，可以将不同的任务（如定型和部署）组合到管道中。 管道可以并行运行，也可以依序运行，能够形成可靠的自动化机制。 有关详细信息，请参阅[使用 Azure 机器学习服务生成机器学习管道](concept-ml-pipelines.md)一文。

可使用 Azure 机器学习 SDK、Azure CLI 或 Azure 门户创建计算目标。 也可以使用现有计算目标，具体方法是将它们添加（附加）到工作区。

> [!IMPORTANT]
> 无法将现有 Azure 容器实例附加到工作区。 必须改为新建实例。
>
> 无法在工作区中创建 Azure HDInsight、Azure Databricks 或 Azure Data Lake Store。 必须先创建资源，然后将其附加到工作区。

## <a name="workflow"></a>工作流

使用 Azure 机器学习开发和部署模型的工作流步骤如下：

1. 使用 Python 编写机器学习定型脚本。
1. 创建并配置计算目标，或附加现有计算目标。
1. 将定型脚本提交到计算目标。
1. 检查结果以找到最佳模型。
1. 在模型注册表中注册模型。
1. 部署模型。

> [!IMPORTANT]
> 定型脚本未绑定到特定计算目标。 最初可以在本地计算机上定型模型，然后无需重写定型脚本，即可切换计算目标。

若要从一个计算目标切换到另一个计算目标，需要创建[运行配置](concept-azure-machine-learning-architecture.md#run-configuration)。 运行配置定义了如何在计算目标上运行脚本。

## <a name="training-scripts"></a>定型脚本

开始执行定型运行任务后，便会提交包含定型脚本的整个目录。 系统会创建快照，并将它发送到计算目标。 有关详细信息，请参阅[快照](concept-azure-machine-learning-architecture.md#snapshot)。

## <a id="local"></a>本地计算机

运行本地定型时，使用 SDK 提交定型操作。 可使用用户管理的环境或系统管理的环境进行定型。

### <a name="user-managed-environment"></a>用户管理的环境

在用户管理的环境中，你负责确保自己选择用来运行脚本的 Python 环境中包含所有必需包。 下面的代码片段示例展示了如何为用户管理的环境配置定型：

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

有关在用户管理环境中演示训练的 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb)。
  
### <a name="system-managed-environment"></a>系统管理的环境

系统管理的环境依赖 conda 管理依赖项。 conda 创建包含依赖项列表的 `conda_dependencies.yml` 文件。 然后，可以让系统生成新 conda 环境，并在其中运行脚本。 只要 `conda_dependencies.yml` 文件保持不变，稍后便能重用系统管理的环境。 

新环境的初始设置可能需要几分钟才能完成，具体视相应依赖项的大小而定。 下面的代码片段展示了如何创建依赖 scikit-learn 的系统管理的环境：

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

有关在系统管理环境中演示训练的 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local/02.train-on-local.ipynb)。

## <a id="dsvm"></a>Data Science Virtual Machine

本地计算机可能没有定型模型所需的计算资源或 GPU 资源。 在这种情况下，可添加其他计算目标（如 Data Science Virtual Machine (DSVM)），纵向或横向扩展定型流程。

> [!WARNING]
> Azure 机器学习仅支持运行 Ubuntu 的虚拟机。 创建虚拟机或选择现有虚拟机时，必须选择运行 Ubuntu 的虚拟机。

若要使用 SDK 将 Data Science Virtual Machine (DSVM) 配置为定型目标，请按以下步骤操作：

1. 创建或附加 Virtual Machine
    
    * 若要新建 DSVM，请先检查是否有同名 DSVM。确认没有后，再新建 VM：
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * 若要附加现有虚拟机作为计算目标，必须提供虚拟机的完全限定域名、登录名和密码。  在下面的示例中，将 ```<fqdn>``` 替换为 VM 的公共完全限定的域名或公共 IP 地址。 将 ```<username>``` 和 ```<password>``` 分别替换为 VM 的 SSH 用户名和密码：

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. 若要在完成后删除计算资源，请运行下面的代码：

    ```python
    dsvm_compute.delete()
    ```

有关在 Data Science Virtual Machine 上演示训练的 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb)。

## <a id="batch"></a>Azure Batch AI

如果模型定型耗时很长，可使用 Azure Batch AI 跨云中的计算资源群集分配定型。 还可以将 Batch AI 配置为启用 GPU 资源。

下面的示例按名称查找现有 Batch AI 群集。 如果找不到，它就会创建一个：

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
batchai_cluster_name = os.environ.get("BATCHAI_CLUSTER_NAME", ws.name + "gpu")
cluster_min_nodes = os.environ.get("BATCHAI_CLUSTER_MIN_NODES", 1)
cluster_max_nodes = os.environ.get("BATCHAI_CLUSTER_MAX_NODES", 3)
vm_size = os.environ.get("BATCHAI_CLUSTER_SKU", "STANDARD_NC6")
autoscale_enabled = os.environ.get("BATCHAI_CLUSTER_AUTOSCALE_ENABLED", True)


if batchai_cluster_name in ws.compute_targets():
    compute_target = ws.compute_targets()[batchai_cluster_name]
    if compute_target and type(compute_target) is BatchAiCompute:
        print('found compute target. just use it. ' + batchai_cluster_name)
else:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = autoscale_enabled,
                                                                cluster_min_nodes = cluster_min_nodes, 
                                                                cluster_max_nodes = cluster_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

必须提供 Azure 资源 ID，才能附加现有 Batch AI 群集作为计算目标。 若要从 Azure 门户获取资源 ID，请使用以下步骤：
1. 在“所有服务”下搜索“`Batch AI`”服务
1. 单击群集所属的工作区名称
1. 选择群集
1. 单击“属性”
1. 复制 **ID**

下面的示例使用 SDK 将群集附加到工作区。 在下面的示例中，将 `<name>` 替换为任何计算名称。 此名称不必与群集名称匹配。 将 `<resource-id>` 替换为上面详述的 Azure 资源 ID：

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

还可以运行下面的 Azure CLI 命令，检查 Batch AI 群集和作业状态：

- 检查群集状态。 可通过运行 `az batchai cluster list` 查看正在运行的节点数。
- 检查作业状态。 可通过运行 `az batchai job list` 查看正在运行的作业数。

创建 Batch AI 群集大约需要 5 分钟。

有关在 Batch AI 群集中演示训练的 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb)。

## <a name='aci'></a>Azure 容器实例 (ACI)

Azure 容器实例是独立容器，优点是启动时间更短，并且无需用户管理任何 Virtual Machine。 Azure 机器学习服务使用 westus、eastus、westeurope、northeurope、westus2 和 southeastasia 区域中的 Linux 容器。 有关详细信息，请参阅[区域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability)。 

下面的示例展示了如何使用 SDK 创建 ACI 计算目标，并使用计算目标定型模型： 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

创建 ACI 计算目标可能需要几秒钟到几分钟。

有关在 Azure 容器实例中演示训练的 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci/03.train-on-aci.ipynb)。

## <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 在使用 Azure 机器学习管道训练模型时，可将 Azure Databricks 用作计算目标。

> [!IMPORTANT]
> Azure Databricks 计算目标只能在机器学习管道中使用。
>
> 必须先创建 Azure Databricks 工作区，然后使用它来训练模型。 若要创建这些资源，请参阅[在 Azure Databricks 中运行 Spark 作业](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)文档。

若要将 Azure Databricks 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __资源 ID__：Azure Databricks 工作区的资源 ID。 以下文本示范了此值的格式：

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.Databricks/workspaces/<databricks-workspace-name>
    ```

    > [!TIP]
    > 若要获取资源 ID，请使用以下 Azure CLI 命令。 请将 `<databricks-ws>` 替换为 Databricks 工作区的名称：
    > ```azurecli-interactive
    > az resource list --name <databricks-ws> --query [].id
    > ```

* __访问令牌__：用于对 Azure Databricks 进行身份验证的访问令牌。 若要生成访问令牌，请参阅[身份验证](https://docs.azuredatabricks.net/api/latest/authentication.html)文档。

以下代码演示如何将 Azure Databricks 附加为计算目标：

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_resource_id = os.environ.get("AML_DATABRICKS_RESOURCE_ID", "<databricks_resource_id>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_resource_id {}'.format(databricks_resource_id))
    print('databricks_access_token {}'.format(databricks_access_token))
    databricks_compute = DatabricksCompute.attach(
             workspace=ws,
             name=databricks_compute_name,
             resource_id=databricks_resource_id,
             access_token=databricks_access_token
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 云中的大数据分析平台。 在使用 Azure 机器学习管道训练模型时，可将 Azure Databricks 用作计算目标。

> [!IMPORTANT]
> Azure Data Lake Analytics 计算目标只能在机器学习管道中使用。
>
> 必须先创建 Azure Data Lake Analytics 帐户，然后使用它来训练模型。 若要创建此资源，请参阅 [Azure Data Lake Analytics 入门](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)文档。

若要将 Data Lake Analytics 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __资源 ID__：Data Lake Analytics 帐户的资源 ID。 以下文本示范了此值的格式：

    ```text
    /subscriptions/<your_subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.DataLakeAnalytics/accounts/<datalakeanalytics-name>
    ```

    > [!TIP]
    > 若要获取资源 ID，请使用以下 Azure CLI 命令。 请将 `<datalakeanalytics>` 替换为你的 Data Lake Analytics 帐户名称：
    > ```azurecli-interactive
    > az resource list --name <datalakeanalytics> --query [].id
    > ```

以下代码演示如何将 Data Lake Analytics 附加为计算目标：

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_id = os.environ.get("AML_ADLA_RESOURCE_ID", "<adla_resource_id>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_id))
    adla_compute = AdlaCompute.attach(
             workspace=ws,
             name=adla_compute_name,
             resource_id=adla_resource_id
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure 机器学习管道只能处理 Data Lake Analytics 帐户的默认数据存储中存储的数据。 如果需要处理的数据不在默认存储中，可以在训练之前使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 复制数据。

## <a id="hdinsight"></a>附加 HDInsight 群集 

HDInsight 是用于大数据分析的热门平台。 它提供的 Apache Spark 可用于定型模型。

> [!IMPORTANT]
> 必须先创建 HDInsight 群集，才能使用它定型模型。 若要在 HDInsight 群集中创建 Spark，请参阅[在 HDInsight 中创建 Spark 群集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)一文。
>
> 创建群集时，必须指定 SSH 用户名和密码。 请记下这些值，因为在将 HDInsight 用作计算目标时需要用到这些值。
>
> 创建后，群集便有完全限定的域名 (FQDN) `<clustername>.azurehdinsight.net`，其中 `<clustername>` 是你为群集命名的名称。 必须有此地址（或群集的公共 IP 地址），才能将群集用作计算目标

必须提供 HDInsight 群集的完全限定的域名、群集登录名和密码，才能将 HDInsight 配置为计算目标。 下面的示例使用 SDK 将群集附加到工作区。 在下面的示例中，将 `<fqdn>` 替换为群集的公共完全限定的域名或公共 IP 地址。 将 `<username>` 和 `<password>` 替换为群集的 SSH 用户名和密码：

> [!NOTE]
> 若要查找群集的 FQDN，请访问 Azure 门户，并选择 HDInsight 群集。 在“概览”部分中，FQDN 是“URL”条目的一部分。 只需删除值开头的 `https://` 即可。
>
> ![HDInsight 群集“概览”部分的屏幕截图，其中突出显示了“URL”条目](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>提交定型运行任务

可通过两种方式提交训练运行：

* 提交 `ScriptRunConfig` 对象。
* 提交 `Pipeline` 对象。

> [!IMPORTANT]
> Azure Databricks、Azure Datalake Analytics 和 Azure HDInsight 计算目标只能在管道中使用。
> 本地计算目标不能在管道中使用。

### <a name="submit-using-scriptrunconfig"></a>使用 `ScriptRunConfig` 提交

无论计算目标是什么，通过 `ScriptRunConfig` 提交训练运行的代码模式都是相同的：

* 使用计算目标的运行配置，创建 `ScriptRunConfig` 对象。
* 提交运行任务。
* 等待运行任务完成。

下面的示例使用了本文档前面创建的系统管理的本地计算目标的配置：

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

有关使用 Spark on HDInsight 演示训练的 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark/05.train-in-spark.ipynb)。

### <a name="submit-using-a-pipeline"></a>使用管道提交

无论计算目标是什么，通过管道提交训练运行的代码模式都是相同的：

* 将步骤添加到计算资源的管道。
* 使用管道提交运行。
* 等待运行任务完成。

以下示例使用前面在本文档中创建的 Azure Databricks 计算目标：

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

有关机器学习管道的详细信息，请参阅[管道和 Azure 机器学习](concept-ml-pipelines.md)文档。

有关演示如何使用管道进行训练的示例 Jupyter Notebook，请参阅 [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline)。

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>使用 Azure 门户查看和设置计算目标

可以在 Azure 门户中查看与工作区关联的计算目标。 若要转到列表，请按以下步骤操作：

1. 访问 [Azure 门户](https://portal.azure.com)，并转到工作区。
2. 单击“应用程序”部分下的“计算”链接。

    ![查看“计算”选项卡](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>创建计算目标

按照上述步骤操作以查看计算目标列表后，请按以下步骤操作来创建计算目标：

1. 单击加号“+”，以添加计算目标。

    ![添加计算 ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. 输入计算目标的名称。
1. 选择要为“定型”附加的计算类型。 

    > [!IMPORTANT]
    > 并非所有计算类型都可以使用 Azure 门户来创建。 目前，可为训练创建的类型包括：
    > 
    > * 虚拟机
    > * Batch AI

1. 选择“新建”，并填写必填窗体字段。 
1. 选择“创建”
1. 可选择列表中的计算目标，查看创建操作的状态。

    ![查看“计算”列表](./media/how-to-set-up-training-targets/View_list.png)然后会看到相应计算目标的详细信息。
    ![查看详细信息](./media/how-to-set-up-training-targets/vm_view.PNG)
1. 现在可以针对这些目标提交运行任务了，如上所详述。

### <a name="reuse-existing-compute-in-your-workspace"></a>重用工作区中的现有计算目标

按照上述步骤操作以查看计算目标列表后，请按以下步骤操作来重用计算目标：

1. 单击加号“+”，以添加计算目标。
2. 输入计算目标的名称。
3. 选择要为“定型”附加的计算类型。

    > [!IMPORTANT]
    > 并非所有计算类型都可以使用门户来附加。
    > 目前，可为训练附加的类型包括：
    > 
    > * 虚拟机
    > * Batch AI

1. 选择“使用现有”。
    - 附加 Batch AI 群集时，从下拉列表中选择计算目标，依次选择 Batch AI 工作区和 Batch AI 群集，再单击“创建”。
    - 附加 Virtual Machine 时，输入 IP 地址、用户名/密码组合、私钥/公钥和端口，再单击“创建”。

    > [!NOTE]
    > Microsoft 建议使用 SSH 密钥，因为它们比密码更安全。 密码易受暴力攻击，而 SSH 密钥依赖的是加密签名。 若要了解如何创建用于 Azure 虚拟机的 SSH 密钥，请参阅以下文档：
    >
    > * [在 Linux 或 macOS 上创建和使用 SSH 密钥]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上创建和使用 SSH 密钥]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. 可通过选择列表中的计算目标来查看预配状态。
6. 现在可以针对这些目标提交运行任务了。

## <a name="examples"></a>示例
下面的笔记本展示了本文中的概念：
* [01.getting-started/02.train-on-local/02.train-on-local.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/02.train-on-local)
* [01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/04.train-on-remote-vm)
* [01.getting-started/03.train-on-aci/03.train-on-aci.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/03.train-on-aci)
* [01.getting-started/05.train-in-spark/05.train-in-spark.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/05.train-in-spark)
* [tutorials/01.train-models.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/01.train-models.ipynb)

若要获取这些笔记本，请执行以下操作：[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [Azure 机器学习 SDK 参考](http://aka.ms/aml-sdk)
* [教程：定型模型](tutorial-train-models-with-aml.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 Azure 机器学习服务生成机器学习管道](concept-ml-pipelines.md)
