---
title: '创建定型 & 部署 (Python 计算) '
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习 Python SDK 创建用于机器学习的训练和部署计算资源（计算目标）
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc7ca9d217e405b0b39779cf256edcf0669afd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302428"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>使用 Python SDK 创建用于模型训练和部署的计算目标

本文中使用 Azure 机器学习 Python SDK 创建和管理计算目标。 也可以使用以下工作室与扩展来创建和管理计算目标：
* [Azure 机器学习工作室](how-to-create-attach-compute-studio.md)， 
* 用于 Azure 机器学习的 [CLI 扩展](reference-azure-machine-learning-cli.md#resource-management)
* 用于 Azure 机器学习的 [VS Code 扩展](how-to-manage-resources-vscode.md#compute-clusters)。


## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)
* [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="limitations"></a>限制

* 不要从工作区**创建多个同时同时与相同计算的附件**。 例如，使用两个不同的名称将一个 Azure Kubernetes Service 群集附加到工作区。 每个新附件都会破坏先前存在的附件。

    如果要重新连接计算目标，例如更改 TLS 或其他群集配置设置，则必须先删除现有附件。

* 本文档中列出的某些方案将标记为 " __预览__"。 提供的预览功能不带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="whats-a-compute-target"></a>什么是计算目标？

使用 Azure 机器学习可以在不同的资源或环境（统称为[__计算目标__](concept-azure-machine-learning-architecture.md#compute-targets)）中训练模型。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。  还可以为模型部署创建计算目标，如[“部署模型的位置和方式”](how-to-deploy-and-where.md)中所述。

## <a name="compute-targets-for-training"></a>训练的计算目标

Azure 机器学习为不同的计算目标提供不同的支持。 典型的模型开发生命周期从开发/试验少量的数据开始。 在此阶段，我们建议使用本地环境。 例如，本地计算机或基于云的 VM。 针对更大的数据集扩展训练或执行分布式训练时，我们建议使用 Azure 机器学习计算来创建可在每次提交运行时自动缩放的单节点或多节点群集。 你也可以附加自己的计算资源，不过，为各种方案提供的支持可能有所不同，详情如下：

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure 机器学习计算群集可以创建为持久资源，也可以在你请求某个运行时动态创建。 基于运行的创建在训练运行完成后会删除计算目标，因此无法重复使用以这种方式创建的计算目标。

使用以下部分配置这些计算目标：

* [本地计算机](#local)
* [Azure 机器学习计算群集](#amlcompute)
* [Azure 机器学习计算实例](#instance)
* [远程虚拟机](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>用于推理的计算目标

执行推理时，Azure 机器学习会创建一个 Docker 容器，用于托管模型以及使用该模型所需的关联资源。 然后，在以下部署方案之一中使用此容器：

* 作为用于实时推理的 __web 服务__ 。 Web 服务部署使用以下计算目标之一：

    * [本地计算机](#local)
    * [Azure 机器学习计算实例](#instance)
    * [Azure 容器实例](#aci)
    * [Azure Kubernetes 服务](how-to-create-attach-kubernetes.md)
    *  (预览版) Azure Functions。 部署到 Azure Functions 仅依赖于 Azure 机器学习来生成 Docker 容器。 在该处，它是使用 Azure Functions 部署的。 有关详细信息，请参阅 [将机器学习模型部署到 Azure Functions (预览版) ](how-to-deploy-functions.md)。

* 用作 __批处理推理__ 终结点，用于定期处理数据批。 批处理推断使用 [Azure 机器学习计算群集](#amlcompute)。

* 对于 __IoT 设备__ (预览) 。 部署到 IoT 设备仅依赖于 Azure 机器学习来构建 Docker 容器。 在该处，它是使用 Azure IoT Edge 部署的。 有关详细信息，请参阅 [ (preview) 部署为 IoT Edge 模块 ](/azure/iot-edge/tutorial-deploy-machine-learning)。

## <a name="local-computer"></a><a id="local"></a>本地计算机

使用本地计算机进行 **培训**时，无需创建计算目标。  只需从本地计算机[提交训练运行](how-to-set-up-training-targets.md)。

使用本地计算机进行 **推断**时，必须安装 Docker。 若要执行部署，请使用 [Deploy_configuration LocalWebservice ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) 来定义 web 服务将使用的端口。 然后按照使用 [Azure 机器学习部署模型](how-to-deploy-and-where.md)中所述，使用常规部署过程。

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure 机器学习计算群集

Azure 机器学习计算群集是一个托管的计算基础结构，可让你轻松创建单节点或多节点计算。 该计算是在工作区区域内部创建的，是可与工作区中的其他用户共享的资源。 提交作业时，计算会自动扩展，并可以放入 Azure 虚拟网络。 计算在容器化环境中执行，将模型的依赖项打包在 [Docker 容器](https://www.docker.com/why-docker)中。

您可以使用 Azure 机器学习计算来跨一个 CPU 群集或云中的 GPU 计算节点分布定型或批处理推理过程。 有关包括 GPU 的 VM 大小的详细信息，请参阅 [GPU 优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。 

Azure 机器学习计算对可以分配的核心数等属性实施默认限制。 有关详细信息，请参阅[管理和请求 Azure 资源的配额](how-to-manage-quotas.md)。

> [!TIP]
> 一般情况下，只要所需核心数方面的配额足够，群集就可以扩展到多达 100 个节点。 默认情况下，设置群集时会启用群集节点之间的通信（例如，为了支持 MPI 作业）。 但是，可以将群集扩展到数千个节点，只需[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)并请求将你的订阅、工作区或特定群集加入允许列表以禁用节点间通信即可。 

可在不同的运行中重复使用 Azure 机器学习计算。 计算可与工作区中的其他用户共享，并在每次运行之后保留，它会根据提交的运行数以及群集上设置的 max_nodes 自动纵向扩展或缩减节点。 min_nodes 设置控制可用节点数的下限。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **创建和附加**：若要在 Python 中创建持久性 Azure 机器学习计算资源，请指定 **vm_size** 和 **max_nodes** 属性。 然后，Azure 机器学习将对其他属性使用智能默认值。 计算在不使用时自动缩减为零个节点。   按需创建专用 VM 来运行作业。
    
    * **vm_size**：Azure 机器学习计算创建的节点的 VM 系列。
    * **max_nodes**：在 Azure 机器学习计算中运行作业时自动扩展到的最大节点数。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   还可以在创建 Azure 机器学习计算时配置多个高级属性。 使用这些属性可以创建固定大小的持久性群集，或者在订阅中的现有 Azure 虚拟网络内创建持久性群集。  有关详细信息，请参阅 [AmlCompute 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true)。

    或者，可以[在 Azure 机器学习工作室](how-to-create-attach-compute-studio.md#portal-create)中创建并附加持久性 Azure 机器学习计算资源。

附加计算后，下一步是 [提交定型运行](how-to-set-up-training-targets.md) 或 [运行批处理推理](how-to-use-parallel-run-step.md)。

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 降低计算群集成本

也可以选择使用[低优先级 VM](concept-plan-manage-cost.md#low-pri-vm) 来运行部分或全部工作负载。 这些 VM 的可用性未得到保证，在使用时可能会被占用。 对于被占用的作业，在重新可用时将重新启动，而不是直接续用。 

请使用以下任一方法指定低优先级 VM：
    
* 在工作室中，在创建 VM 时选择“低优先级”****。
    
* 使用 Python SDK，在预配配置中设置 `vm_priority` 属性。  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* 使用 CLI 设置 `vm-priority`：
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 设置托管标识

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

在预配配置中配置托管标识：  
    
* 系统分配的托管标识：
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* 用户分配的托管标识： 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

将托管标识添加到现有计算群集：  
    
* 系统分配的托管标识：

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* 用户分配的托管标识：

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>托管标识用法

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Azure 机器学习计算实例

[Azure 机器学习计算实例](concept-compute-instance.md)是一个托管的计算基础结构，可让你轻松创建单个 VM。 该计算是在工作区区域内创建的，但与计算群集不同，实例不能与工作区中的其他用户共享。 此外，实例不会自动纵向缩减。  你必须停止该资源，防止连续不断地产生费用。

计算实例可以并行运行多个作业，它有一个作业队列。 

计算实例可以在[虚拟网络环境](how-to-enable-virtual-network.md#compute-instance)中安全地运行作业，无需企业打开 SSH 端口。 作业在容器化环境中执行，并将模型依赖项打包到 Docker 容器中。 

1. **创建和附加**： 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

现在，你已附加了计算并配置了运行，下一步是 [提交定型运行](how-to-set-up-training-targets.md) 或 [部署模型以进行推理](how-to-deploy-local-container-notebook-vm.md)。

## <a name="azure-container-instance"></a><a id="aci"></a>Azure 容器实例

部署模型时，动态创建 (ACI) 的 Azure 容器实例。 不能以任何其他方式创建和将 ACI 附加到工作区。 有关详细信息，请参阅 [将模型部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes 服务

当与 Azure 机器学习一起使用时，Azure Kubernetes Service (AKS) 允许使用各种配置选项。 有关详细信息，请参阅 [如何创建和附加 Azure Kubernetes 服务](how-to-create-attach-kubernetes.md)。

## <a name="remote-virtual-machines"></a><a id="vm"></a>远程虚拟机

Azure 机器学习还支持将自己的计算资源附加到工作区。 任意远程 VM（只要可从 Azure 机器学习访问）都是这种资源类型。 该资源可以是 Azure VM，也可以是组织内部或本地的远程服务器。 具体而言，在指定 IP 地址和凭据（用户名和密码，或 SSH 密钥）的情况下，可以使用任何可访问的 VM 进行远程运行。

可以使用系统生成的 conda 环境、现有的 Python 环境或 Docker 容器。 若要在 Docker 容器中执行，必须在 VM 上运行 Docker 引擎。 需要一个比本地计算机更灵活的基于云的开发/试验环境时，此功能特别有用。

请对此方案使用 Data Science Virtual Machine (DSVM) 作为 Azure VM。 此 VM 在 Azure 中预配置了数据科学和 AI 开发环境。 此 VM 提供精选的工具和框架用于满足整个机器学习开发生命周期的需求。 有关如何将 DSVM 与 Azure 机器学习配合使用的详细信息，请参阅[配置开发环境](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)。

1. **创建**：创建 DSVM，然后使用它来训练模型。 若要创建此资源，请参阅[预配适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

    > [!WARNING]
    > Azure 机器学习仅支持运行 Ubuntu 的虚拟机。 创建 VM 或选择现有 VM 时，必须选择使用 Ubuntu 的 VM。
    > 
    > Azure 机器学习还要求虚拟机具有公共 IP 地址。

1. **附加**：若要附加现有虚拟机作为计算目标，必须提供虚拟机的资源 ID、用户名和密码。 可以使用订阅 ID、资源组名称和 VM 名称按以下字符串格式构造 VM 的资源 ID：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   或者，可以[使用 Azure 机器学习工作室](how-to-create-attach-compute-studio.md#attached-compute)将 DSVM 附加到工作区。

    > [!WARNING]
    > 不要从工作区为同一 DSVM 创建多个同时同步的附件。 每个新附件都会破坏先前存在的附件。

1. **配置**：为 DSVM 计算目标创建运行配置。 Docker 与 conda 用于在 DSVM 上创建和配置训练环境。

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

附加计算并配置运行后，下一步是[提交训练运行](how-to-set-up-training-targets.md)。

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是用于大数据分析的热门平台。 该平台提供的 Apache Spark 可用于训练模型。

1. **创建**：先创建 HDInsight 群集，然后使用它来训练模型。 若要在 HDInsight 群集中创建 Spark，请参阅[在 HDInsight 中创建 Spark 群集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。 

    > [!WARNING]
    > Azure 机器学习要求 HDInsight 群集具有公共 IP 地址。

    创建群集时，必须指定 SSH 用户名和密码。 请记下这些值，因为在将 HDInsight 用作计算目标时需要用到这些值。
    
    创建群集后，使用主机名 \<clustername>-ssh.azurehdinsight.net 连接到该群集，其中，\<clustername> 是为该群集提供的名称。 

1. **附加**：若要将 HDInsight 群集附加为计算目标，必须提供该 HDInsight 群集的资源 ID、用户名和密码。 可以使用订阅 ID、资源组名称和 HDInsight 群集名称按以下字符串格式构造 HDInsight 群集的资源 ID：`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   或者，可以[使用 Azure 机器学习工作室](how-to-create-attach-compute-studio.md#attached-compute)将 HDInsight 群集附加到工作区。

    > [!WARNING]
    > 不要从工作区为同一 HDInsight 创建多个同时同步的附件。 每个新附件都会破坏先前存在的附件。

1. **配置**：为 HDI 计算目标创建运行配置。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


附加计算并配置运行后，下一步是[提交训练运行](how-to-set-up-training-targets.md)。

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch 用于在云中高效运行大规模并行高性能计算 (HPC) 应用程序。 可以在 Azure 机器学习管道中使用 AzureBatchStep 将作业提交到 Azure Batch 计算机池。

若要将 Azure Batch 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

-    **Azure Batch 计算名称**：在工作区中用于计算的易记名称
-    **Azure Batch 帐户名称**：Azure Batch 帐户的名称
-    **资源组**：包含 Azure Batch 帐户的资源组。

以下代码演示如何将 Azure Batch 附加为计算目标：

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> 不要同时从工作区创建同一 Azure Batch 的多个附件。 每个新附件都会破坏先前存在的附件。

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它可以用作 Azure 机器学习管道的计算目标。

请先创建 Azure Databricks 工作区，然后再使用该工作区。 若要创建工作区资源，请参阅[在 Azure Databricks 中运行 Spark 作业](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)文档。

要将 Azure Databricks 附加为计算目标，请提供以下信息：

* __Databricks 计算名称__：要分配给此计算资源的名称。
* __Databricks 工作区名称__：Azure Databricks 工作区的名称。
* __Databricks 访问令牌__：用于对 Azure Databricks 进行身份验证的访问令牌。 若要生成访问令牌，请参阅[身份验证](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)文档。

以下代码演示如何使用 Azure 机器学习 SDK 将 Azure Databricks 附加为计算目标（Databricks 工作区需要与 AML 工作区位于同一个订阅中）：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

有关更详细的示例，请参阅 GitHub 上的 [示例笔记本](https://aka.ms/pl-databricks)。

> [!WARNING]
> 不要同时从工作区创建同一 Azure Databricks 的多个附件。 每个新附件都会破坏先前存在的附件。

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 云中的大数据分析平台。 它可以用作 Azure 机器学习管道的计算目标。

使用该平台之前，请先创建 Azure Data Lake Analytics 帐户。 若要创建此资源，请参阅 [Azure Data Lake Analytics 入门](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)文档。

若要将 Data Lake Analytics 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __资源组__：包含 Data Lake Analytics 帐户的资源组。
* __帐户名称__：Data Lake Analytics 帐户名。

以下代码演示如何将 Data Lake Analytics 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

有关更详细的示例，请参阅 GitHub 上的 [示例笔记本](https://aka.ms/pl-adla)。

> [!WARNING]
> 不要从工作区为同一 ADLA 创建多个同时同步的附件。 每个新附件都会破坏先前存在的附件。

> [!TIP]
> Azure 机器学习管道只能处理 Data Lake Analytics 帐户的默认数据存储中存储的数据。 如果需要处理的数据不在默认存储中，可以在训练之前使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) 复制数据。

## <a name="notebook-examples"></a>Notebook 示例

请参阅以下 Notebook，其中提供了有关使用各种计算目标进行训练的示例：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* 使用计算资源来 [配置和提交定型运行](how-to-set-up-training-targets.md)。
* [教程：训练模型](tutorial-train-models-with-aml.md)使用一个托管计算目标来训练模型。
* 若要构建更好的模型，请了解如何[高效地优化超参数](how-to-tune-hyperparameters.md)。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
