---
title: 为模型训练创建和使用计算目标
titleSuffix: Azure Machine Learning
description: 为机器学习模型训练配置训练环境（计算目标）。 可以轻松地在训练环境之间切换。 在本地开始训练。 如果需要横向扩展，请切换到基于云的计算目标。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 95ded3c184836ac58a0f97d1bf30dd2e3c123ccb
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755973"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>设置并使用模型定型的计算目标 

使用 Azure 机器学习，可以在各种资源或环境（统称为[__计算目标__](concept-azure-machine-learning-architecture.md#compute-targets)）上训练模型。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。  还可以为模型部署创建计算目标，如[“部署模型的位置和方式”](how-to-deploy-and-where.md)中所述。

您可以使用 Azure 机器学习 SDK、Azure 门户、工作区登陆页面（预览）、Azure CLI 或 Azure 机器学习 VS Code 扩展来创建和管理计算目标。 如果计算目标是通过其他服务（例如 HDInsight 群集）创建的，则可以通过将其附加到 Azure 机器学习工作区来使用它们。
 
本文介绍如何使用各种计算目标进行模型训练。  适用于所有计算目标的步骤遵循相同的工作流：
1. __创建__计算目标（如果没有）。
2. 将计算目标__附加__到工作区。
3. __配置__计算目标，使其包含脚本所需的 Python 环境和包依赖项。


>[!NOTE]
> 本文中的代码已使用 Azure 机器学习 SDK 版本 1.0.39 进行测试。

## <a name="compute-targets-for-training"></a>训练的计算目标

Azure 机器学习在不同的计算目标之间具有不同的支持。 典型的模型开发生命周期从开发/试验少量的数据开始。 在此阶段，我们建议使用本地环境。 例如，本地计算机或基于云的 VM。 针对更大的数据集扩展训练或执行分布式训练时，我们建议使用 Azure 机器学习计算来创建可在每次提交运行时自动缩放的单节点或多节点群集。 你也可以附加自己的计算资源，不过，为各种方案提供的支持可能有所不同，详情如下：

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure 机器学习计算资源可以创建为持久资源，也可以在你请求运行时动态创建。 基于运行的创建在训练运行完成后会删除计算目标，因此无法重复使用以这种方式创建的计算目标。

## <a name="whats-a-run-configuration"></a>什么是运行配置？

训练时，通常会在本地计算机上开始，然后在不同的计算目标上运行该训练脚本。 通过 Azure 机器学习，你可以在不同的计算目标上运行脚本，而无需更改脚本。

只需在**运行配置**中为每个计算目标定义环境。  然后，当你想要在不同的计算目标上运行训练试验时，可以指定该计算的运行配置。 有关指定环境并将其绑定到运行配置的详细信息，请参阅[创建和管理用于定型和部署的环境](how-to-use-environments.md)。

本文的最后详细介绍了如何[提交试验](#submit)。

## <a name="whats-an-estimator"></a>估计器是什么？

为了便于使用常见框架进行模型训练，Azure 机器学习 Python SDK 提供了一个替代级别更高的抽象方法，即估计器类。 此类使你能够轻松地构造运行配置。 您可以创建和使用一般[估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)来提交使用您选择的任何学习框架的培训脚本（如 scikit-learn）。

对于 PyTorch、TensorFlow 和 Chainer 任务，Azure 机器学习还提供相应的[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、 [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)和[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)估算以简化使用这些框架的操作。

有关详细信息，请参阅[用估算训练 ML 模型](how-to-train-ml-models.md)。

## <a name="whats-an-ml-pipeline"></a>什么是 ML 管道？

借助 ML 管道，你可以通过简单、速度、可移植性和重复使用优化工作流。 当通过 Azure 机器学习构建管道时，可以专注于专业技能、机器学习，而不是在基础结构和自动化上。

ML 管道是从多个**步骤**构造的，这些步骤是管道中的不同计算单元。 每个步骤都可以独立运行并使用独立的计算资源。 这允许多个数据科学家在同一时间同时处理同一管道，而不会产生过多的计算资源，同时还可以轻松地对每个步骤使用不同的计算类型/大小。

> [!TIP]
> 在训练模型时，ML 管道可以使用运行配置或估算。

虽然 ML 管道可以训练模型，但它们还可以在训练和部署模型之后准备数据。 管道的主要用例之一是批处理评分。 有关详细信息，请参阅[管道：优化机器学习工作流](concept-ml-pipelines.md)。

## <a name="set-up-in-python"></a>在 Python 中设置

使用以下部分配置这些计算目标：

* [本地计算机](#local)
* [Azure 机器学习计算](#amlcompute)
* [远程虚拟机](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>本地计算机

1. **创建和附加**：无需创建或附加计算目标即可使用本地计算机作为训练环境。  

1. **配置**：将本地计算机用作计算目标时，将在[开发环境](how-to-configure-environment.md)中运行训练代码。  如果该环境已包含所需的 Python 包，请使用用户管理的环境。

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

附加计算并配置运行后，下一步是[提交训练运行](#submit)。

### <a id="amlcompute"></a>Azure 机器学习计算

Azure 机器学习计算是一个托管的计算基础结构，可让用户轻松创建单节点或多节点计算。 该计算是在工作区区域内部创建的，是可与工作区中的其他用户共享的资源。 提交作业时，计算会自动扩展，并可以放入 Azure 虚拟网络。 计算在容器化环境中执行，将模型的依赖项打包在 [Docker 容器](https://www.docker.com/why-docker)中。

可以使用 Azure 机器学习计算在云中的 CPU 或 GPU 计算节点群集之间分配训练进程。 有关包括 GPU 的 VM 大小的详细信息，请参阅 [GPU 优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

Azure 机器学习计算对可以分配的核心数等属性实施默认限制。 有关详细信息，请参阅[管理和请求 Azure 资源的配额](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)。


可以在计划运行时按需创建 Azure 机器学习计算环境，或者将其创建为持久性资源。

#### <a name="run-based-creation"></a>基于运行的创建

可将 Azure 机器学习计算创建为运行时的计算目标。 将自动为运行创建计算。 完成运行后，会自动删除计算。 

> [!NOTE]
> 若要指定要使用的最大节点数，通常应将 `node_count` 设置为节点数。 当前有（04/04/2019） bug 阻止了此操作。 解决方法是使用运行配置的 `amlcompute._cluster_max_node_count` 属性。 例如，`run_config.amlcompute._cluster_max_node_count = 5` 。

> [!IMPORTANT]
> Azure 机器学习计算的基于运行的创建功能目前为预览版。 如果使用自动化超参数优化或自动化机器学习，请不要使用基于运行的创建。 若要使用超参数优化或自动化机器学习，请改为创建[持久性计算](#persistent)目标。

1.  **创建、附加和配置**：基于运行的创建执行创建、附加和配置运行配置的计算目标所需的所有步骤。  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


附加计算并配置运行后，下一步是[提交训练运行](#submit)。

#### <a id="persistent"></a>持久性计算

可在不同的作业中重复使用持久性 Azure 机器学习计算。 计算可与工作区中的其他用户共享，完成每个作业后可以保留。

1. **创建和附加**：若要在 Python 中创建持久性 Azure 机器学习计算资源，请指定**vm_size**和**max_nodes**属性。 然后，Azure 机器学习将对其他属性使用智能默认值。 计算在不使用时自动缩减为零个节点。   按需创建专用 VM 来运行作业。
    
    * **vm_size**： Azure 机器学习计算创建的节点的 vm 系列。
    * **max_nodes**：在 Azure 机器学习计算上运行作业时最多可自动缩放的节点数。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   还可以在创建 Azure 机器学习计算时配置多个高级属性。 使用这些属性可以创建固定大小的持久性群集，或者在订阅中的现有 Azure 虚拟网络内创建持久性群集。  有关详细信息，请参阅 [AmlCompute 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )。
    
   或者，可以[在 Azure 门户中](#portal-create)创建并附加持久性 Azure 机器学习计算资源。

1. **配置**：为永久性计算目标创建运行配置。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

附加计算并配置运行后，下一步是[提交训练运行](#submit)。


### <a id="vm"></a>远程虚拟机

Azure 机器学习还支持将自己的计算资源附加到工作区。 这种类型的资源类型是任意远程 VM，只要可从 Azure 机器学习访问。 该资源可以是 Azure VM，也可以是组织内部或本地的远程服务器。 具体而言，在指定 IP 地址和凭据（用户名和密码，或 SSH 密钥）的情况下，可以使用任何可访问的 VM 进行远程运行。

可以使用系统生成的 conda 环境、现有的 Python 环境或 Docker 容器。 若要在 Docker 容器中执行，必须在 VM 上运行 Docker 引擎。 需要一个比本地计算机更灵活的基于云的开发/试验环境时，此功能特别有用。

请对此方案使用 Data Science Virtual Machine (DSVM) 作为 Azure VM。 此 VM 在 Azure 中预配置了数据科学和 AI 开发环境。 此 VM 提供精选的工具和框架用于满足整个机器学习开发生命周期的需求。 有关如何将 DSVM 与 Azure 机器学习配合使用的详细信息，请参阅[配置开发环境](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)。

1. **Create**：创建 DSVM，然后使用它来训练模型。 若要创建此资源，请参阅[预配适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)。

    > [!WARNING]
    > Azure 机器学习仅支持运行 Ubuntu 的虚拟机。 创建 VM 或选择现有 VM 时，必须选择使用 Ubuntu 的 VM。

1. **附加**：若要将现有的虚拟机附加为计算目标，必须为虚拟机提供完全限定的域名（FQDN）、用户名和密码。 在本示例中，请将 \<fqdn> 替换为 VM 的 FQDN，或替换为公共 IP 地址。 请将 \<username> 和 \<password> 替换为 VM 的 SSH 用户名和密码。

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   或者，可以[使用 Azure 门户](#portal-reuse)将 DSVM 附加到工作区。

1. **配置**：创建 DSVM 计算目标的运行配置。 Docker 与 conda 用于在 DSVM 上创建和配置训练环境。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


附加计算并配置运行后，下一步是[提交训练运行](#submit)。

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight 是用于大数据分析的热门平台。 该平台提供的 Apache Spark 可用于训练模型。

1. **Create**：创建 HDInsight 群集，然后使用它来训练模型。 若要在 HDInsight 群集中创建 Spark，请参阅[在 HDInsight 中创建 Spark 群集](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)。 

    创建群集时，必须指定 SSH 用户名和密码。 请记下这些值，因为在将 HDInsight 用作计算目标时需要用到这些值。
    
    创建群集后，使用主机名 \<clustername>-ssh.azurehdinsight.net 连接到该群集，其中，\<clustername> 是为该群集提供的名称。 

1. **附加**：若要将 hdinsight 群集附加为计算目标，必须提供 hdinsight 群集的主机名、用户名和密码。 下面的示例使用 SDK 将群集附加到工作区。 在该示例中，请将 \<clustername> 替换为群集名称。 请将 \<username> 和 \<password> 替换为群集的 SSH 用户名和密码。

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
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

   或者，可以[使用 Azure 门户](#portal-reuse)将 HDInsight 群集附加到工作区。

1. **配置**：创建 HDI 计算目标的运行配置。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


附加计算并配置运行后，下一步是[提交训练运行](#submit)。


### <a id="azbatch"></a>Azure Batch 

Azure Batch 用于在云中高效运行大规模并行和高性能计算（HPC）应用程序。 可以在 Azure 机器学习管道中使用 AzureBatchStep 将作业提交到 Azure Batch 计算机池。

若要将 Azure Batch 附加为计算目标，必须使用 Azure 机器学习 SDK，并提供以下信息：

-   **Azure Batch 计算名称**：要用于工作区中计算的友好名称
-   **Azure Batch 帐户名称**： Azure Batch 帐户的名称
-   **资源组**：包含 Azure Batch 帐户的资源组。

下面的代码演示如何将 Azure Batch 附加为计算目标：

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

## <a name="set-up-in-azure-portal"></a>在 Azure 门户中设置

可以在 Azure 门户中访问与工作区关联的计算目标。  可以使用门户执行以下操作：

* [查看](#portal-view)附加到工作区的计算目标
* 在工作区中[创建计算目标](#portal-create)
* [附加](#portal-reuse)在工作区外部创建的计算目标


创建目标并将其附加到工作区后，可以通过一个 `ComputeTarget` 对象在运行配置中使用该目标： 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>查看计算目标


若要查看工作区的计算目标，请使用以下步骤：

1. 导航到 [Azure 门户](https://portal.azure.com)，并打开你的工作区。 你还可以在[工作区登陆页面（预览版）](https://ml.azure.com)中访问这些相同的步骤，尽管下面的图像显示了 Azure 门户。
 
1. 在“应用程序”下，选择“计算”。

    [![View 计算 "选项卡](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>创建计算目标

遵循上述步骤查看计算目标的列表。 然后使用以下步骤创建计算目标： 

1. 选择加号 (+) 添加计算目标。

    ![添加计算目标](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. 输入计算目标的名称。 

1. 选择“机器学习计算”作为用于训练的计算类型。 

    >[!NOTE]
    >Azure 机器学习计算是可在 Azure 门户中创建的唯一一种托管计算资源。  创建其他所有计算资源后，可以附加这些资源。

1. 填写表单。 提供必需属性的值，尤其是“VM 系列”，以及用于运转计算的**最大节点数**。  

1. 选择__创建__。


1. 通过在列表中选择计算目标来查看创建操作的状态：

    ![选择一个计算目标以查看创建操作的状态](./media/how-to-set-up-training-targets/View_list.png)

1. 然后会看到相应计算目标的详细信息： 

    ![查看计算目标详细信息](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>附加计算目标

若要使用在 Azure 机器学习工作区之外创建的计算目标，必须附加它们。 附加计算目标会使其可供你的工作区使用。

遵循上述步骤查看计算目标的列表。 然后使用以下步骤来附加计算目标： 

1. 选择加号 (+) 添加计算目标。 
1. 输入计算目标的名称。 
1. 选择要为训练附加的计算类型：

    > [!IMPORTANT]
    > 并非所有计算类型都可以从 Azure 门户附加。 目前，可为训练附加的计算类型包括：
    >
    > * 远程 VM
    > * Azure Databricks（在机器学习管道中使用）
    > * Azure Data Lake Analytics（在机器学习管道中使用）
    > * Azure HDInsight

1. 填写表单，并提供必需属性的值。

    > [!NOTE]
    > Microsoft 建议使用 SSH 密钥，因为它们比密码更安全。 密码很容易受到暴力破解攻击。 SSH 密钥依赖于加密签名。 若要了解如何创建用于 Azure 虚拟机的 SSH 密钥，请参阅以下文档：
    >
    > * [在 Linux 或 macOS 上创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 选择“附加”。 
1. 通过在列表中选择计算目标来查看附加操作的状态。

## <a name="set-up-with-cli"></a>设置 CLI

您可以使用 Azure 机器学习的[CLI 扩展](reference-azure-machine-learning-cli.md)访问与工作区关联的计算目标。  可以使用 CLI 执行以下操作：

* 创建托管计算目标
* 更新托管计算目标
* 附加托管计算目标

有关详细信息，请参阅[资源管理](reference-azure-machine-learning-cli.md#resource-management)。

## <a name="set-up-with-vs-code"></a>设置 VS Code

你可以使用 Azure 机器学习的[VS Code 扩展](how-to-vscode-tools.md#create-and-manage-compute-targets)来访问、创建和管理与工作区关联的计算目标。

## <a id="submit"></a>使用 Azure 机器学习 SDK 提交培训运行

创建运行配置后，可以使用它来运行试验。  对于所有类型的计算目标，用于提交训练运行的代码模式都是相同的：

1. 创建要运行的试验
1. 提交运行任务。
1. 等待运行任务完成。

> [!IMPORTANT]
> 提交训练运行时，将创建包含定型脚本的目录的快照，并将其发送到计算目标。 它也作为实验的一部分存储在工作区中。 如果更改文件并再次提交运行，则只会上载已更改的文件。
>
> 若要防止文件包含在快照中，请在目录中创建一个[.gitignore](https://git-scm.com/docs/gitignore)或 `.amlignore` 文件，并将文件添加到其中。 @No__t_0 文件使用与[.gitignore](https://git-scm.com/docs/gitignore)文件相同的语法和模式。 如果这两个文件都存在，则 `.amlignore` 文件优先。
> 
> 有关详细信息，请参阅[快照](concept-azure-machine-learning-architecture.md#snapshots)。

### <a name="create-an-experiment"></a>创建试验

首先，在工作区中创建一个试验。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>提交试验

使用 `ScriptRunConfig` 对象提交该试验。  此对象包含：

* **source_directory**：包含定型脚本的源目录
* **脚本**：标识训练脚本
* **run_config**：运行配置，该配置又定义了定型发生的位置。

例如，若要使用[本地目标](#local)配置：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

使用不同的运行配置（例如 [amlcompute 目标](#amlcompute)）将同一试验切换为在不同的计算目标中运行：

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> 此示例默认为仅使用计算目标的一个节点进行定型。 若要使用多个节点，请将运行配置的 `node_count` 设置为所需的节点数。 例如，下面的代码将用于定型的节点数设置为4：
>
> ```python
> src.run_config.node_count = 4
> ```

或者可以：

* 根据[使用评估器训练机器学习模型](how-to-train-ml-models.md)中所述，使用 `Estimator` 对象提交试验。
* 提交用于[超参数优化](how-to-tune-hyperparameters.md)的 HyperDrive 运行。
* 通过[VS Code 扩展](how-to-vscode-tools.md#train-and-tune-models)提交试验。

有关详细信息，请参阅[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py)和[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)文档。

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>使用 Azure 机器学习 CLI 创建运行配置并提交运行

您可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)和[机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)来创建运行配置，并将运行中的运行提交到不同的计算目标。 以下示例假定你已有 Azure 机器学习工作区，并且已使用 `az login` CLI 命令登录到 Azure。 

### <a name="create-run-configuration"></a>创建运行配置

创建运行配置的最简单方法是导航包含机器学习 Python 脚本的文件夹，并使用 CLI 命令

```azurecli
az ml folder attach
```

此命令创建一个子文件夹 `.azureml`，其中包含不同计算目标的模板运行配置文件。 可以复制和编辑这些文件，以自定义配置，例如添加 Python 包或更改 Docker 设置。  

### <a name="structure-of-run-configuration-file"></a>运行配置文件的结构

运行配置文件的格式 YAML，以下部分
 * 要运行的脚本及其参数
 * 计算目标名称，可以是 "本地"，也可以是工作区中计算的名称。
 * 用于执行运行的参数：框架，用于分布式运行的 communicator，最大持续时间和计算节点数。
 * 环境部分。 有关本部分中的字段的详细信息，请参阅[创建和管理用于培训和部署的环境](how-to-use-environments.md)。
   * 若要指定要为运行安装的 Python 包，请创建[conda 环境文件](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)，并设置__condaDependenciesFile__字段。
 * 运行历史记录详细信息以指定日志文件文件夹，以及启用或禁用输出收集和运行历史记录快照。
 * 特定于所选框架的配置详细信息。
 * 数据引用和数据存储的详细信息。
 * 用于创建新群集的机器学习计算的特定配置详细信息。

### <a name="create-an-experiment"></a>创建试验

首先，为您的运行创建一个试验

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>脚本运行

若要提交脚本运行，请执行命令

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive 运行

可以将 HyperDrive 与 Azure CLI 结合使用来执行参数优化运行。 首先，创建以下格式的 HyperDrive 配置文件。 有关超参数优化参数的详细信息，请参阅[优化模型的超参数](how-to-tune-hyperparameters.md)一文。

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

将此文件与运行配置文件一起添加。 然后使用以下内容提交 HyperDrive 运行：
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

请注意 .runconfig 和 HyperDrive config 中的*参数空间*中的*参数*部分。它们包含要传递给训练脚本的命令行参数。 .Runconfig 中的值在每次迭代中保持不变，而 HyperDrive config 中的范围将循环访问。 不要在这两个文件中指定相同的参数。

有关这些 ```az ml``` CLI 命令和完整参数集的更多详细信息，请参阅[参考文档](reference-azure-machine-learning-cli.md)。

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git 跟踪和集成

当你开始在源目录为本地 Git 存储库的训练运行时，有关存储库的信息存储在运行历史记录中。 有关详细信息，请参阅[Git integration for Azure 机器学习](concept-train-model-git-integration.md)。

## <a name="notebook-examples"></a>Notebook 示例

请参阅以下 Notebook，其中提供了有关使用各种计算目标进行训练的示例：
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* [教程：定型模型](tutorial-train-models-with-aml.md)使用托管计算目标来定型模型。
* 了解如何[有效地调整超参数](how-to-tune-hyperparameters.md)以构建更好的模型。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* 查看 [RunConfiguration 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 参考。
* [在 Azure 虚拟网络中使用 Azure 机器学习](how-to-enable-virtual-network.md)
