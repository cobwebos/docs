---
title: Azure 快速入门 - 使用 Batch AI 进行 CNTK 训练 - Python | Microsoft Docs
description: 快速了解如何通过 Python SDK 使用 Batch AI 运行 CNTK 训练作业
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: f535c9adf4926f29ae9cade6382debedab73937d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>通过 Azure Python SDK 运行 CNTK 训练作业

本快速入门详述如何使用 Azure Python SDK 通过 Batch AI 服务来运行 Microsoft Cognitive Toolkit (CNTK) 训练作业。 

在此示例中，请使用包含手绘图像的 MNIST 数据库在单节点 GPU 群集上训练一个卷积神经网络 (CNN)。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

* Azure Python SDK - 请参阅[安装说明](/python/azure/python-sdk-azure-install)

* Azure 存储帐户 - 请参阅[如何创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)

* Azure Active Directory 服务主体凭据 - 请参阅[如何使用 CLI 创建服务主体](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* 使用 Azure Cloud Shell 或 Azure CLI 针对订阅注册 Batch AI 资源提供程序一次。 提供程序注册最多可能需要 15 分钟。

  ```azurecli
  az provider register -n Microsoft.BatchAI
  az provider register -n Microsoft.Batch
  ```


## <a name="configure-credentials"></a>配置凭据
在 Python 脚本中创建这些参数，用你自己的值进行替换：

```Python
# credentials used for authentication
client_id = 'my_aad_client_id'
secret = 'my_aad_secret_key'
token_uri = 'my_aad_token_uri'
subscription_id = 'my_subscription_id'

# credentials used for storage
storage_account_name = 'my_storage_account_name'
storage_account_key = 'my_storage_account_key'

# specify the credentials used to remote login your GPU node
admin_user_name = 'my_admin_user_name'
admin_user_password = 'my_admin_user_password'
```

最佳做法是将所有凭据存储在单独的配置文件中，但这未在本示例中演示。 若要实施某个配置文件，请参阅[诀窍](https://github.com/Azure/BatchAI/tree/master/recipes)。 

## <a name="authenticate-with-batch-ai"></a>通过 Batch AI 进行身份验证

需要使用 Azure Active Directory 进行身份验证，然后才能访问 Azure Batch AI。 下面的代码使用服务主体凭据和订阅 ID 通过服务进行身份验证（创建 `BatchAIManagementClient` 对象）：

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=client_id, secret=secret, token_uri=token_uri)

batchai_client = batchai.BatchAIManagementClient(credentials=creds,
                                         subscription_id=subscription_id
)
```

## <a name="create-a-resource-group"></a>创建资源组

Batch AI 群集和作业是 Azure 资源，必须放置在 Azure 资源组中。 以下代码片段创建一个资源组：

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'

resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)

resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>准备 Azure 文件共享
为了进行演示，本快速入门使用 Azure 文件共享托管学习作业的训练数据和脚本。 

1. 创建名为 *batchaiquickstart* 的文件共享。

  ```Python
  from azure.storage.file import FileService 
 
  azure_file_share_name = 'batchaiquickstart' 
 
  service = FileService(storage_account_name, storage_account_key) 
 
  service.create_share(azure_file_share_name, fail_on_exist=False)
  ``` 
 
2. 在名为 *mnistcntksample* 的共享中创建目录 

  ```Python
  mnist_dataset_directory = 'mnistcntksample' 
 
  service.create_directory(azure_file_share_name, mnist_dataset_directory, fail_on_exist=False) 
  ```
3. 下载[示例包](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)并将其解压缩。 将内容上传到执行 Python 脚本时所在的目录中。

  ```Python
  for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt', 'ConvNet_MNIST.py']:     
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f) 
  ```

## <a name="create-gpu-cluster"></a>创建 GPU 群集

创建 Batch AI 群集。 在此示例中，群集包含单个 STANDARD_NC6 VM 节点。 此 VM 大小有一个 NVIDIA K80 GPU。 在名为 *azurefileshare* 的文件夹中装载文件共享。 此文件夹在 GPU 计算节点上的完整路径为 $AZ_BATCHAI_MOUNT_ROOT/azurefileshare。

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare' 
 
parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus', 
 
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6', 
 
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
         admin_user_name=admin_user_name,
         admin_user_password=admin_user_password), 
 
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
         manual=models.ManualScaleSettings(target_node_count=1)
     ), 
 
    # Configure each node in the cluster
    node_setup=models.NodeSetup( 
 
        # Mount shared volumes to the host
         mount_volumes=models.MountVolumes(
             azure_file_shares=[
                 models.AzureFileShareReference(
                     account_name=storage_account_name,
                     credentials=models.AzureStorageCredentialsInfo(
         account_key=storage_account_key),
         azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
               storage_account_name, azure_file_share_name),
                  relative_mount_path = relative_mount_point)],
         ), 
    ), 
) 
batchai_client.clusters.create(resource_group_name, cluster_name, parameters).result() 
```

## <a name="get-cluster-status"></a>获取群集状态

使用以下命令监视群集状态： 

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count)) 
```

上述代码输出基本的群集分配信息，例如：

```Shell
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving 0
 
```  

当节点处于已分配状态且完成准备以后，群集会处于就绪状态（参见 `nodeStateCounts` 属性）。 如果出错，`errors` 属性会包含错误说明。

## <a name="create-training-job"></a>创建训练作业

群集准备就绪之后，请配置并提交学习作业。 

```Python
job_name = 'myjob' 
 
parameters = models.job_create_parameters.JobCreateParameters( 
 
     # Location where the job will run
     # Ideally this should be co-located with the cluster.
     location='eastus', 
 
     # The cluster this job will run on
     cluster=models.ResourceId(cluster.id), 
 
     # The number of VMs in the cluster to use
     node_count=1, 
 
     # Override the path where the std out and std err files will be written to.
     # In this case we will write these out to an Azure Files share
     std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point), 
 
     input_directories=[models.InputDirectory(
         id='SAMPLE',
         path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(relative_mount_point, mnist_dataset_directory))], 
 
     # Specify directories where files will get written to 
     output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")], 
 
     # Container configuration
     container_settings=models.ContainerSettings(
        models.ImageSourceRegistry(image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')), 
 
     # Toolkit specific settings
     cntk_settings = models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
 ) 
 
# Create the job 
batchai_client.jobs.create(resource_group_name, job_name, parameters).result() 
```

## <a name="monitor-job"></a>监视作业
可以使用以下命令检查作业的状态： 
 
```Python
job = batchai_client.jobs.get(resource_group_name, job_name) 
 
print('Job state: {0} '.format(job.execution_state.name))
```

输出类似于：`Job state: running`。

`executionState` 包含作业的当前执行状态：
* `queued`：作业正等待群集节点变得可用
* `running`：作业正在运行
* `succeeded`（或 `failed`）：作业已完成，`executionInfo` 包含有关结果的详细信息
 
## <a name="list-stdout-and-stderr-output"></a>列出 stdout 和 stderr 输出
使用以下命令列出 stdout 和 stderr 日志文件的链接：

```Python
files = batchai_client.jobs.list_output_files(resource_group_name, job_name, models.JobsListOutputFilesOptions("stdouterr")) 
 
for file in list(files):
     print('file: {0}, download url: {1}'.format(file.name, file.download_url)) 
```
## <a name="delete-resources"></a>删除资源

使用以下命令删除作业：
```Python
batchai_client.jobs.delete(resource_group_name, job_name) 
```

使用以下命令删除群集：
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```
## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure Python SDK 在 Batch AI 群集上运行 CNTK 训练作业。 若要详细了解如何通过不同的工具包来使用 Batch AI，请参阅[训练诀窍](https://github.com/Azure/BatchAI)。
