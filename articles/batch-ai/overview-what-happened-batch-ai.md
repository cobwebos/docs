---
title: Azure Batch AI 将发生什么情况？ | Microsoft Docs
description: 了解 Azure Batch AI 和 Azure 机器学习服务计算选项发生的情况。
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: 87dcf18a2517561e3166726f8f1f1a70c2ec7713
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447797"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI 将发生什么情况？

Azure Batch AI 服务即将在 3 月停用。 Batch AI 的大规模培训和评分功能现在可在 [Azure 机器学习服务](../machine-learning/service/overview-what-is-azure-ml.md)中获取，该服务已于 2018 年 12 月 4 日正式发布。

除了许多其他机器学习功能外，Azure 机器学习服务还包括基于云的托管计算目标，用于培训、部署和对机器学习模型评分。 此计算目标称为 [Azure 机器学习计算](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。 [开始迁移并立即使用](#migrate)。 可以通过 Azure 机器学习服务 [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、命令行界面和 [Azure 门户](../machine-learning/service/quickstart-get-started.md)与 Azure 机器学习服务进行交互。

## <a name="support-timeline"></a>支持时间线

| 日期 | Batch AI 服务支持的详细信息 |
| ---- |-----------------|
| December&nbsp;14&#x2c;&nbsp;2018| 可以像以前一样继续使用现有的 Azure Batch AI 订阅。 但是，无法注册**新的订阅**，并且我们不会对此服务进行新的投资。|
| March&nbsp;31&#x2c;&nbsp;2019 | 此日期之后，现有的 Batch AI 订阅不再有效。 |

## <a name="how-does-azure-machine-learning-service-compare"></a>Azure 机器学习服务与 Batch AI 服务之间的比较
Azure 机器学习服务是一项云服务，可以使用它来训练、部署、自动执行以及管理机器学习模型，所有这些都是在云提供的广泛范围内进行的。 [在此概述文章中大致了解 Azure 机器学习服务](../machine-learning/service/overview-what-is-azure-ml.md)。
 

典型的模型开发生命周期涉及到数据准备、训练和试验以及部署阶段。 可以使用机器学习管道协调此端到端周期。

![流程图](./media/overview-what-happened-batch-ai/lifecycle.png)


[详细了解该服务的工作原理及其主要概念](../machine-learning/service/concept-azure-machine-learning-architecture.md)。 模型训练工作流中的许多概念类似于 Batch AI 中的现有概念。 

具体而言，下面是两者之间的对应关系：
 
|Batch AI 服务|  Azure 机器学习服务|
|:--:|:---:|
|工作区|工作区|
|群集|   `AmlCompute` 类型的计算|
|文件服务器|数据存储|
|试验|试验|
|作业|运行（允许嵌套运行）|
 
下面是上表的另一个视图，可帮助你直观地进一步了解这两个服务：
 
**Batch AI 层次结构**
![流程图](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Azure 机器学习服务层次结构**
![流程图](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>平台功能
Azure 机器学习服务引入了一套强大的新功能，包括从训练到堆栈部署的端到端流程，可用于进行 AI 开发，而无需管理任何 Azure 资源。 下表比较了这两个服务的训练功能支持。

|Feature|Batch AI 服务|Azure 机器学习服务|
|-------|:-------:|:-------:|
|VM 大小选项 |CPU/GPU    |CPU/GPU。 此外支持用于推断的 FPGA|
|AI 就绪的群集（驱动程序、Docker 等等。）|  是 |是|
|节点准备| 是|    否|
|OS 系列选项   |部分    |否|
|专用和低优先级 VM  |是    |是|
|自动缩放   |是    |是（默认支持）|
|自动缩放等待时间  |否 |是|
|SSH    |是|   是|
|群集级装载 |是（文件共享、Blob、NFS、自定义）   |是（装载或下载数据存储）|
|分布式训练|  是 |是|
|作业执行模式|    VM 或容器|    容器|
|自定义容器映像|    是 |是|
|任何工具包    |是    |是（运行 Python 脚本）|
|作业准备|    是 |尚不支持|
|作业级装载 |是（文件共享、Blob、NFS、自定义）   |是（文件共享、Blob）|
|作业监视     |通过 GetJob|    通过运行历史记录（更丰富的信息、自定义运行时可推送更多指标）|
|检索作业日志和文件/模型 |   通过列表文件和存储 API  |通过项目服务|
 |Tensorboard 支持   |否|    是|
|VM 系列级配额 |是    |是（使用以前余留的容量）|
 
除了上表中列出的功能以外，Azure 机器学习服务中的某些功能在传统上不受 Batch AI 的支持。

|Feature|Batch AI 服务|Azure 机器学习服务|
|-------|:-------:|:-------:|
|环境准备    |否 |是（Conda 准备和上传到 ACR）|
|超参数优化  |否|    是|
|模型管理   |否 |是|
|操作化/部署| 否  |通过 AKS 和 ACI|
|数据准备   |否 |是|
|计算目标    |Azure VM  |本地、Batch AI（作为 AmlCompute）、DataBricks、HDInsight|
|自动化机器学习 |否|    是|
|管道  |否 |是|
|批量评分  |是    |是|
|门户/CLI 支持|    是 |是|


### <a name="programming-interfaces"></a>编程接口

下表列出了适用于每个服务的各种编程接口。
    
|Feature|Batch AI 服务|Azure 机器学习服务|
|-------|:-------:|:-------:|
|SDK 中 IsInRole 中的声明    |Java、C#、Python、Nodejs   |Python（两个服务都对常用框架运行基于配置和基于估算器的接口）|
|CLI    |是    |尚不支持|
|Azure 门户   |是    |是（作业提交除外）|
|REST API   |是    |是，但跨微服务分布|




## <a name="why-migrate"></a>为何要迁移？

从 Batch AI 服务预览版升级到 Azure 机器学习服务正式版后，可以通过估算器和数据存储等更易用的概念来获得更好的体验。 此外，还能保证获得正式版 Azure 服务的 SLA 保障和客户支持。

Azure 机器学习服务还引入了自动化机器学习、超参数优化和机器学习管道等新功能，在大部分的大规模 AI 工作负荷中，这些功能非常有用。 无需切换到单独的服务即可将训练的模型操作化，这有助于完成整个数据科学循环：从准备数据（使用数据准备 SDK），到操作化和模型监视。

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>如何迁移？

在执行本迁移指南中的步骤以帮助在两个服务之间映射命令之前，我们建议花点时间，通过 Azure 机器学习服务的[文档](../machine-learning/service/overview-what-is-azure-ml.md)（包括 [Python 教程](../machine-learning/service/tutorial-train-models-with-aml.md)）来熟悉该服务。

要避免应用程序中断并确保可使用最新功能，请在 2019 年 3 月 31 日之前执行以下步骤：

1. 创建 Azure 机器学习服务工作区并开始使用：
    + [基于 Python 的快速入门](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [基于 Azure 门户的快速入门](../machine-learning/service/quickstart-get-started.md)

1. 为模型定型设置 [Azure 机器学习计算](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。

1. 更新脚本以使用 Azure 机器学习计算。


### <a name="sdk-migration"></a>SDK 迁移

Azure 机器学习服务中的当前 SDK 支持是通过多个 Python SDK 实现的。 主要 SDK 大约每隔两周更新一次，可使用以下命令通过 PyPi 安装：

```python
pip install --upgrade azureml-sdk[notebooks]
```

参考这些[快速入门说明](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)设置环境并安装 SDK

打开包含指向相关 conda 环境的内核的 Jupyter Notebook 后，下面是这两个服务中的命令的映射方式：


#### <a name="create-a-workspace"></a>创建工作区
Batch AI 中使用 configuration.json 初始化工作区的概念类似于在 Azure 机器学习中使用配置文件。

在 **Batch AI** 中使用的代码如下：

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

在 **Azure 机器学习服务**中，可以尝试：

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

此外，还可以通过指定配置参数来直接创建工作区，如下所示

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

在 [SDK 参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)中详细了解 AML 工作区类。


#### <a name="create-a-compute-cluster"></a>创建计算群集
Azure 机器学习支持多个计算目标，其中的某些目标由附加到工作区的不同服务（例如 HDInsight 群集或远程 VM）管理。 详细了解各种[计算目标](../machine-learning/service/how-to-set-up-training-targets.md)。 创建 Batch AI 计算群集的概念对应于在 Azure 机器学习中创建 AmlCompute 群集。 创建 Amlcompute 采用计算配置，这类似于在 Batch AI 中传递参数。 需要注意的一点是，自动缩放在 AmlCompute 群集上默认已启用，而在 Batch AI 中默认已禁用。

在 **Batch AI** 中使用的代码如下：

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

在 **Azure 机器学习服务**中，可以尝试：

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

在 [SDK 参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)中详细了解 AMLCompute 类。 请注意，在上述配置中，只有 vm_size 和 max_nodes 是必需的，剩余的属性（例如 VNet）仅适用于高级群集设置。


#### <a name="monitoring-status-of-your-cluster"></a>监视群集的状态
如下所示，在 Azure 机器学习中可以更直接地监视群集状态。

在 **Batch AI** 中使用的代码如下：

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

在 **Azure 机器学习服务**中，可以尝试：

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>获取对存储帐户的引用
在 Azure 机器学习中，Blob 等数据存储的概念已使用数据存储对象进行简化。 默认情况下，Azure 机器学习工作区会创建存储帐户，但你也可以在创建工作区的过程中附加自己的存储。 

在 **Batch AI** 中使用的代码如下：

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

在 **Azure 机器学习服务**中，可以尝试：

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

在 [Azure 机器学习服务文档](../machine-learning/service/how-to-access-data.md#create-a-datastore)中详细了解如何注册其他存储帐户，或获取对另一个已注册的数据存储的引用。


#### <a name="downloading-and-uploading-data"></a>下载和上传数据 
在任一服务中，都可以使用上述数据存储引用轻松将数据上传到存储帐户。 在 Batch AI 中，我们还会部署训练脚本作为文件共享的一部分，不过，在 Azure 机器学习中，可将训练脚本指定为作业配置的一部分。

在 **Batch AI** 中使用的代码如下：

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


在 **Azure 机器学习服务**中，可以尝试：

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>创建试验
如前所述，类似于 Batch AI，Azure 机器学习中存在试验的概念。 每个试验可以包含不同的运行，类似于 Batch AI 中的作业。 Azure 机器学习还允许针对每个父运行下的各个子运行创建层次结构。

在 **Batch AI** 中使用的代码如下：

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

在 **Azure 机器学习服务**中，可以尝试：

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>提交作业
创建试验后，可通过多种不同的方式提交运行。 此示例尝试使用 TensorFlow 创建深度学习模型，并使用 Azure 机器学习估算器来执行该操作。 [估算器](../machine-learning/service/how-to-train-ml-models.md)只是基础运行配置中的一个包装器函数，它可以简化运行的提交，目前仅受 Pytorch 和 TensorFlow 的支持。 通过数据存储的概念，还可以轻松指定装载路径 

在 **Batch AI** 中使用的代码如下：

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

在 Batch AI 中提交作业本身的过程是通过 create 函数完成的。

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

在 [Batch AI 示例 Notebook github 存储库](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed)中可以找到有关此训练代码片段的完整信息（包括我们已上传到上述文件共享的 mnist_replica.py 文件）。

在 **Azure 机器学习服务**中，可以尝试：

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

在 [Azure 机器学习示例 Notebook github 存储库](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server)中可以找到有关此训练代码片段的完整信息（包括 tf_mnist_replica.py 文件）。 数据存储本身可以装载在单个节点上，或者，可以在节点本身上下载训练数据。 [Azure 机器学习服务文档](../machine-learning/service/how-to-access-data.md#access-datastores-for-training)中提供了有关在估算器中引用数据存储的更多详细信息。 

在 Azure 机器学习中提交运行的过程是通过 submit 函数完成的。

```python
run = experiment.submit(estimator)
print(run)
```

可通过另一种方式指定运行的参数，即使用运行配置 – 定义自定义训练环境时，此方法尤其有用。 可以在此[示例 AmlCompute Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb) 中找到更多详细信息。 

#### <a name="monitor-your-run"></a>监视运行
提交运行后，可以等待该运行完成，或者使用可直接从代码中调用的简洁 Jupyter 小组件，在 Azure 机器学习中对其进行监视。 此外，可以通过循环访问工作区中的各个试验以及每个试验中的各个运行，来提取以前的任一运行的上下文。

在 **Batch AI** 中使用的代码如下：

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


在 **Azure 机器学习服务**中，可以尝试：

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

以下快照演示小组件如何载入 Notebook 来实时查看日志：![小组件监视图](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="editing-a-cluster"></a>编辑群集
删除群集的过程非常直截了当。 此外，如果你想要将某个群集扩展为包含更多的节点，或者在缩减群集之前增大空闲等待时间，Azure 机器学习还允许你从 Notebook 内部更新该群集。 我们不允许更改群集本身的 VM 大小，因为这需要在后端有效地进行新的部署。

在 **Batch AI** 中使用的代码如下：
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

在 **Azure 机器学习服务**中，可以尝试：

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>支持

我们已计划在 3 月 31 日停用 Batch AI，并且已开始阻止针对该服务注册新的订阅，除非通过支持部门提出例外处理，将相应订阅列入允许列表。  如有任何疑问，或者在迁移到 Azure 机器学习服务的过程中想要提供反馈，请通过 [Azure Batch AI 训练预览版](mailto:AzureBatchAITrainingPreview@service.microsoft.com)联系我们。

Azure 机器学习服务现已推出正式版。 这意味着，它附带了经过承诺的 SLA，并且有多种支持计划可供选择。

通过 Batch AI 服务或 Azure 机器学习服务使用 Azure 基础结构的价格应该是相同的，因为在这两种情况下，我们只收取基础计算的费用。 有关详细详细，请参阅[定价计算器](https://azure.microsoft.com/pricing/details/machine-learning-service/)。

在 [Azure 门户](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all)中查看这两个服务的可用区域。


## <a name="next-steps"></a>后续步骤

+ 请阅读 [Azure 机器学习服务概述](../machine-learning/service/overview-what-is-azure-ml.md)。

+ 请使用 Azure 机器学习服务[为模型定型配置计算目标](../machine-learning/service/how-to-set-up-training-targets.md)。

+ 请查看 [Azure 路线图](https://azure.microsoft.com/updates/)了解其他 Azure 服务更新。
