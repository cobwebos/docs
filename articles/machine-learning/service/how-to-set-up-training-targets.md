---
title: 模型训练的计算目标
titleSuffix: Azure Machine Learning service
description: 为机器学习模型训练配置训练环境（计算目标）。 可以轻松切换训练环境。 请从本地定型入手，如果需要横向扩展，请切换到基于云的计算目标。 Databricks
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338852"
---
# <a name="set-up-compute-targets-for-model-training"></a>设置模型训练的计算目标

使用 Azure 机器学习服务可以在不同的计算资源中训练模型。 这些计算资源称为“计算目标”，可以位于本地，也可以位于云中。 本文档介绍了受支持的计算目标，以及如何使用它们。

计算目标是在其中运行训练脚本的资源，或者在将模型部署为 Web 服务时用于托管模型的位置。 可以使用 Azure 机器学习 SDK、Azure 门户或 Azure CLI 创建和管理计算目标。 如果通过其他服务（例如 HDInsight 群集）创建了计算目标，可以通过将其附加到 Azure 机器学习服务工作区来使用它们。

Azure 机器学习支持的计算目标有三大类别：

* __本地__：本地计算机，或用作开发/试验环境的基于云的 VM。 

* __托管计算__：Azure 机器学习计算是由 Azure 机器学习服务管理的计算产品/服务。 使用它可以轻松创建单节点或多节点计算用于执行训练、测试和批量推断。

* __附加计算__：还可以将你自己的 Azure 云计算附加到 Azure 机器学习。 请阅读下文详细了解支持的计算类型及其用法。


## <a name="supported-compute-targets"></a>受支持的计算目标

Azure 机器学习服务为不同的计算目标提供不同的支持。 典型的模型开发生命周期从开发/试验少量的数据开始。 在此阶段，我们建议使用本地环境。 例如，本地计算机或基于云的 VM。 针对更大的数据集扩展训练或执行分布式训练时，我们建议使用 Azure 机器学习计算来创建可在每次提交运行时自动缩放的单节点或多节点群集。 你也可以附加自己的计算资源，不过，为各种方案提供的支持可能有所不同，详情如下：

|计算目标| GPU 加速 | 自动超参数优化 | 自动化机器学习 | 管道友好|
|----|:----:|:----:|:----:|:----:|
|[本地计算机](#local)| 可能 | &nbsp; | ✓ | &nbsp; |
|[Azure 机器学习计算](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[远程 VM](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks 和 Azure Data Lake Analytics __只能__在管道中使用。 有关管道的详细信息，请参阅 [Azure 机器学习中的管道](concept-ml-pipelines.md)文档。

> [!IMPORTANT]
> 必须从工作区内部创建 Azure 机器学习计算。 不能将现有实例附加到工作区。
>
> 其他计算目标必须在 Azure 机器学习外部创建，然后附加到工作区。

> [!NOTE]
> 训练模型时，某些计算目标依赖于 Docker 容器映像。 GPU 基本映像只能在 Microsoft Azure 服务中使用。 在模型训练中，这些服务包括：
>
> * Azure 机器学习计算
> * Azure Kubernetes 服务
> * Data Science Virtual Machine。

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

> [!TIP]
> 每当通过创建托管计算或附加现有计算将某个计算目标与工作区相关联时，需要提供计算的名称。 此名称的长度应为 2 到 16 个字符。

若要从一个计算目标切换到另一个计算目标，需要创建[运行配置](concept-azure-machine-learning-architecture.md#run-configuration)。 运行配置定义了如何在计算目标上运行脚本。

## <a name="training-scripts"></a>定型脚本

启动训练运行时，会创建包含训练脚本的目录的快照，并将其发送到计算目标。 有关详细信息，请参阅[快照](concept-azure-machine-learning-architecture.md#snapshot)。

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

## <a id="amlcompute"></a>Azure 机器学习计算

Azure 机器学习计算是一个托管的计算基础结构，可让用户轻松创建单节点或多节点计算。 它是在__工作区区域内部__创建的，是可与工作区中的其他用户共享的资源。 提交作业时，它会自动扩展，并可以放入 Azure 虚拟网络。 它__容器化环境__中执行，将模型的依赖项打包在 Docker 容器中。

可以使用 Azure 机器学习计算在云中的 CPU 或 GPU 计算节点群集之间分配训练进程。 有关包括 GPU 的 VM 大小的详细信息，请参阅 [GPU 优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)文档。

> [!NOTE]
> Azure 机器学习计算对可以分配的核心数等属性实施默认限制。 有关详细信息，请参阅[管理和请求 Azure 资源的配额](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)文档。

可以在计划运行时按需创建 Azure 机器学习计算，或者将其创建为持久性资源。

### <a name="run-based-creation"></a>基于运行的创建

可将 Azure 机器学习计算创建为运行时的计算目标。 在这种情况下，计算将自动为运行创建，最大可扩展到在运行配置中指定的 max_nodes，运行完成后会__自动删除__。

> [!IMPORTANT]
> Azure 机器学习计算的基于运行的创建功能目前为预览版。 如果使用超参数优化或自动化机器学习，请不要使用基于运行的创建。 如果需要使用超参数优化或自动化机器学习，请在提交运行之前创建 Azure 机器学习计算。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>持久性计算（基本）

可在多个作业中重复使用持久性 Azure 机器学习计算。 它可以与工作区中的其他用户共享，完成每个作业后可以保留。

若要创建持久性 Azure 机器学习计算资源，请指定 `vm_size` 和 `max_nodes` 参数。 然后，Azure 机器学习将对剩余的参数使用智能默认值。  例如，该计算设置为在不使用时自动缩减为零个节点，并按需创建专用 VM 来运行作业。 

* **vm_size**：Azure 机器学习计算创建的节点的 VM 系列。
* **max_nodes**：在 Azure 机器学习计算上运行作业时自动缩放到的最大节点数。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>持久性计算（高级）

还可以在创建 Azure 机器学习计算时配置多个高级属性。  使用这些属性可以创建固定大小的持久性群集，或者在订阅中的现有 Azure 虚拟网络内创建持久性群集。

除了 `vm_size` 和 `max_nodes` 以外，还可以使用以下属性：

* **min_nodes**：在 Azure 机器学习计算上运行作业时缩减到的最小节点数（默认为 0 个节点）。
* **vm_priority**：创建 Azure 机器学习计算时选择“dedicated”（默认值）或“lowpriority”VM。 低优先级 (lowpriority) VM 使用 Azure 的多余容量，因此成本更低，但存在运行被抢占资源的风险。
* **idle_seconds_before_scaledown**：运行完成之后、自动缩放到 min_nodes 之前等待的空闲时间（默认为 120 秒）。
* **vnet_resourcegroup_name**：__现有__虚拟网络的资源组。 Azure 机器学习计算是在此虚拟网络中创建的。
* **vnet_name**：虚拟网络的名称。 虚拟网络必须与 Azure 机器学习工作区位于同一区域。
* **subnet_name**：虚拟网络中子网的名称。 将为 Azure 机器学习计算资源分配此子网范围内的 IP 地址。

> [!TIP]
> 创建持久性 Azure 机器学习计算资源时，还可以更新其属性，例如 min_nodes 或 max_nodes。 只需对它调用 `update()` 函数即可。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>远程 VM

Azure 机器学习还支持将自己的计算资源附加到工作区。 任意远程 VM 就是这样一种资源类型（前提是可从 Azure 机器学习服务访问它）。 它可以是 Azure VM，也可以是组织内部或本地的远程服务器。 具体而言，在指定 IP 地址和凭据（用户名/密码或 SSH 密钥）的情况下，可以使用任何可访问的 VM 进行远程运行。
可以使用系统生成的 conda 环境、现有的 Python 环境或 Docker 容器。 使用 Docker 容器执行需要在 VM 上运行 Docker 引擎。 需要一个比本地计算机更灵活的基于云的开发/试验环境时，此功能特别有用。

> [!TIP]
> 我们建议对此方案使用 Data Science Virtual Machine 作为 Azure VM。 它在 Azure 中预配置了数据科学和 AI 开发环境，并提供精选的工具和框架用于满足整个机器学习开发生命周期的需要。 有关将 Data Science Virtual Machine 与 Azure 机器学习配合使用的详细信息，请参阅[配置开发环境](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)文档。

> [!WARNING]
> Azure 机器学习仅支持运行 Ubuntu 的虚拟机。 创建虚拟机或选择现有虚拟机时，必须选择运行 Ubuntu 的虚拟机。

若要使用 SDK 将 Data Science Virtual Machine (DSVM) 配置为定型目标，请按以下步骤操作：

1. 若要附加现有虚拟机作为计算目标，必须提供虚拟机的完全限定域名、登录名和密码。  在下面的示例中，将 ```<fqdn>``` 替换为 VM 的公共完全限定的域名或公共 IP 地址。 将 ```<username>``` 和 ```<password>``` 分别替换为 VM 的 SSH 用户名和密码：

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. 创建 DSVM 计算目标的配置。 Docker 与 conda 用于在 DSVM 上创建和配置训练环境：

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

## <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 在使用 Azure 机器学习管道训练模型时，可将 Azure Databricks 用作计算目标。

> [!IMPORTANT]
> Azure Databricks 计算目标只能在机器学习管道中使用。
>
> 必须先创建 Azure Databricks 工作区，然后使用它来训练模型。 若要创建这些资源，请参阅[在 Azure Databricks 中运行 Spark 作业](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)文档。

若要将 Azure Databricks 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __Databricks 工作区名称__：Azure Databricks 工作区的名称。
* __访问令牌__：用于对 Azure Databricks 进行身份验证的访问令牌。 若要生成访问令牌，请参阅[身份验证](https://docs.azuredatabricks.net/api/latest/authentication.html)文档。

以下代码演示如何将 Azure Databricks 附加为计算目标：

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
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
* __资源组__：包含 Data Lake Analytics 帐户的资源组。
* __帐户名__：Data Lake Analytics 帐户名。

以下代码演示如何将 Data Lake Analytics 附加为计算目标：

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure 机器学习管道只能处理 Data Lake Analytics 帐户的默认数据存储中存储的数据。 如果需要处理的数据不在默认存储中，可以在训练之前使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 复制数据。

## <a id="hdinsight"></a>Azure HDInsight 

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
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
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
> Azure Databricks 和 Azure Datalake Analytics 计算目标只能在管道中使用。
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

1. 输入计算目标的名称
1. 选择“机器学习计算”作为用于__训练__的计算类型

    > [!IMPORTANT]
    > 只能创建 Azure 机器学习计算作为用于训练的托管计算

1. 填写所需的窗体，尤其是“VM 系列”以及用于运转计算的最大节点数 
1. 选择“创建”
1. 可以通过在列表中选择计算目标来查看创建操作的状态

    ![查看“计算”列表](./media/how-to-set-up-training-targets/View_list.png)

1. 然后会看到相应计算目标的详细信息。

    ![查看详细信息](./media/how-to-set-up-training-targets/compute-target-details.png)

1. 现在，可按前面的详细针对这些目标提交运行


### <a name="reuse-existing-compute-in-your-workspace"></a>重用工作区中的现有计算目标

按照上述步骤操作以查看计算目标列表后，请按以下步骤操作来重用计算目标：

1. 单击 **+** 添加计算目标
2. 输入计算目标的名称
3. 选择要为__训练__附加的计算类型

    > [!IMPORTANT]
    > 并非所有计算类型都可以使用门户来附加。
    > 目前，可为训练附加的类型包括：
    > 
    > * 远程 VM
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. 填写所需的窗体

    > [!NOTE]
    > Microsoft 建议使用 SSH 密钥，因为它们比密码更安全。 密码易受暴力攻击，而 SSH 密钥依赖的是加密签名。 若要了解如何创建用于 Azure 虚拟机的 SSH 密钥，请参阅以下文档：
    >
    > * [在 Linux 或 macOS 上创建和使用 SSH 密钥]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上创建和使用 SSH 密钥]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 选择“附加”
1. 可以通过在列表中选择计算目标来查看附加操作的状态
1. 现在，可按前面的详细针对这些目标提交运行

## <a name="examples"></a>示例
参阅以下位置的笔记本：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [Azure 机器学习 SDK 参考](https://aka.ms/aml-sdk)
* [教程：训练模型](tutorial-train-models-with-aml.md)
* [模型部署位置](how-to-deploy-and-where.md)
* [使用 Azure 机器学习服务生成机器学习管道](concept-ml-pipelines.md)
