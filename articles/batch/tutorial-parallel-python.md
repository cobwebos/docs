---
title: 运行并行工作负荷 - Azure Batch Python
description: 教程 - 在 Azure Batch 中使用 Batch Python 客户端库通过 ffmpeg 并行处理媒体文件
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a04cd63944d0ed75ff90f211134cd93c77abe1e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>教程：使用 Python API 通过 Azure Batch 运行并行工作负荷

使用 Azure Batch 在 Azure 中高效运行大规模并行和高性能计算 (HPC) 批处理作业。 本教程通过一个 Python 示例演示了如何使用 Batch 运行并行工作负荷。 你可以学习常用的 Batch 应用程序工作流，以及如何以编程方式与 Batch 和存储资源交互。 学习如何：

> [!div class="checklist"]
> * 通过 Batch 和存储帐户进行身份验证
> * 将输入文件上传到存储
> * 创建运行应用程序所需的计算节点池
> * 创建用于处理输入文件的作业和任务
> * 监视任务执行情况
> * 检索输出文件

本教程使用 [ffmpeg](http://ffmpeg.org/) 开源工具将 MP4 媒体文件并行转换为 MP3 格式。 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* [Python 2.7、3.3 或更高版本](https://www.python.org/downloads/)

* [pip](https://pip.pypa.io/en/stable/installing/) 包管理器

* Azure Batch 帐户和关联的常规用途 Azure 存储帐户。 若要创建这些帐户，请参阅 Batch 快速入门（使用 [Azure 门户](quick-create-portal.md)或 [Azure CLI](quick-create-cli.md)）。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>下载并运行示例

### <a name="download-the-sample"></a>下载示例

从 GitHub [下载或克隆示例应用](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial)。 若要使用 Git 客户端克隆示例应用存储库，请使用以下命令：

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

导航到包含 `batch_python_tutorial_ffmpeg.py` 文件的目录。

在 Python 环境中使用 `pip` 安装所需的包。

```bash
pip install -r requirements.txt
```

打开 `batch_python_tutorial_ffmpeg.py` 文件。 使用特定于帐户的值更新 Batch 帐户和存储帐户凭据字符串。 例如：


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>运行应用程序

若要运行该脚本，请执行以下操作：

```
python batch_python_tutorial_ffmpeg.py
```

运行示例应用程序时，控制台输出如下所示。 在执行期间启动池的计算节点时，会遇到暂停并看到`Monitoring all tasks for 'Completed' state, timeout in 00:30:00...`。 
   
```
Sample start: 12/12/2017 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 12/12/2017 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

转到 Azure 门户中的 Batch 帐户，监视池、计算节点、作业和任务。 例如，若要查看池中计算节点的热度地图，请单击“池” > “LinuxFFmpegPool”。

任务正在运行时，热度地图如下所示：

![池热度地图](./media/tutorial-parallel-python/pool.png)

以默认配置运行应用程序时，典型的执行时间大约为 **5 分钟**。 池创建过程需要最多时间。 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>查看代码

以下部分将示例应用程序细分为多个执行步骤，用于处理 Batch 服务中的工作负荷。 在阅读本文的其余内容时，请参阅 Python 代码，因为我们并没有讨论示例中的每行代码。

### <a name="authenticate-blob-and-batch-clients"></a>对 Blob 和 Batch 客户端进行身份验证

为了与存储帐户交互，应用使用 [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) 包来创建 [BlockBlobService](/python/api/azure.storage.blob.blockblobservice.blockblobservice) 对象。

```python
blob_client = azureblob.BlockBlobService(
    account_name=STORAGE_ACCOUNT_NAME,
    account_key=STORAGE_ACCOUNT_KEY)
```

应用创建的 [BatchServiceClient](/python/api/azure.batch.batchserviceclient) 对象用于创建和管理 Batch 服务中的池、作业和任务。 示例中的 Batch 客户端使用共享密钥身份验证。 Batch 还支持通过 [Azure Active Directory](batch-aad-auth.md) 进行身份验证，以便对单个用户或无人参与应用程序进行身份验证。

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
    _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>上传输入文件

应用使用 `blob_client` 引用为输入 MP4 文件创建一个存储容器，并为任务输出创建一个容器。 然后，它会调用 `upload_file_to_container` 函数，将本地 `InputFiles` 目录中的 MP4 文件上传到容器。 存储中的文件定义为 Batch [ResourceFile](/python/api/azure.batch.models.resourcefile) 对象，Batch 随后可以将这些对象下载到计算节点。

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []
    
for folder, subs, files in os.walk('./InputFiles/'):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks. 
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>创建计算节点池

然后，该示例会调用 `create_pool` 以在 Batch 帐户中创建计算节点池。 这个定义的函数使用 Batch [PoolAddParameter](/python/api/azure.batch.models.pooladdparameter) 类来设置节点数、VM 大小和池配置。 在这里，[VirtualMachineConfiguration](/python/api/azure.batch.models.virtualmachineconfiguration) 对象指定对 Azure Marketplace 中发布的 Ubuntu Server 16.04 LTS 映像的 [ImageReference](/python/api/azure.batch.models.imagereference)。 Batch 支持 Azure Marketplace 中的各种 VM 映像以及自定义 VM 映像。

节点数和 VM 大小使用定义的常数进行设置。 Batch 支持专用节点和[低优先级](batch-low-pri-vms.md)节点。可以在池中使用这其中的一种，或者两种都使用。 专用节点为池保留。 低优先级节点在 Azure 有剩余 VM 容量时以优惠价提供。 如果 Azure 没有足够的容量，低优先级节点会变得不可用。 默认情况下，此示例创建的池只包含 5 个大小为 *Standard_A1_v2* 的低优先级节点。 

除了物理节点属性，此池配置还包括 [StartTask](/python/api/azure.batch.models.starttask) 对象。 StartTask 在每个节点加入池以及每次重新启动节点时在该节点上运行。 在此示例中，StartTask 运行的 Bash shell 命令用于在节点上安装 ffmpeg 包和依赖项。

[pool.add](/python/api/azure.batch.operations.pooloperations#azure_batch_operations_PoolOperations_add) 方法将池提交到 Batch 服务。

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>创建作业

Batch 作业可指定在其中运行任务的池以及可选设置，例如工作的优先级和计划。 此示例通过调用 `create_job` 创建一个作业。 这个定义的函数使用 [JobAddParameter](/python/api/azure.batch.models.jobaddparameter) 类在池中创建作业。 [job.add](/python/api/azure.batch.operations.joboperations#azure_batch_operations_JobOperations_add) 方法将池提交到 Batch 服务。 作业一开始没有任务。

```python
job = batch.models.JobAddParameter(
    job_id,
    batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>创建任务

应用通过调用 `add_tasks` 在作业中创建任务。 这个定义的函数使用 [TaskAddParameter](/python/api/azure.batch.models.taskaddparameter) 类创建任务对象的列表。 每个任务都运行 ffmpeg，使用 `command_line` 参数来处理输入 `resource_files` 对象。 ffmpeg 此前已在创建池时安装在每个节点上。 在这里，命令行运行 ffmpeg 将每个输入 MP4（视频）文件转换为 MP3（音频）文件。

此示例在运行命令行后为 MP3 文件创建 [OutputFile](/python/api/azure.batch.models.outputfile) 对象。 每个任务的输出文件（在此示例中为一个）都会使用任务的 `output_files` 属性上传到关联的存储帐户中的一个容器。

然后，应用使用 [task.add_collection](/python/api/azure.batch.operations.taskoperations#azure_batch_operations_TaskOperations_add_collection) 方法将任务添加到作业，使任务按顺序在计算节点上运行。 

```python
tasks = list()

for idx, input_file in enumerate(input_files): 
    input_file_path=input_file.file_path
    output_file_path="".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(output_file_path,
              destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(output_container_sas_url)),
              upload_options=batchmodels.OutputFileUploadOptions(
                batchmodels.OutputFileUploadCondition.task_success))]
            )
     )
batch_service_client.task.add_collection(job_id, tasks)
```    

### <a name="monitor-tasks"></a>监视任务

将任务添加到作业时，Batch 自动对任务排队并进行计划，方便其在关联的池中的计算节点上执行。 Batch 根据指定的设置处理所有任务排队、计划、重试和其他任务管理工作。 

监视任务的执行有许多方法。 此示例中的 `wait_for_tasks_to_complete` 函数使用 [TaskState](/python/api/azure.batch.models.taskstate) 对象监视任务在某个时限内是否出现某种状态（在此示例中为“已完成”状态）。

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

     incomplete_tasks = [task for task in tasks if
                         task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>清理资源

运行任务之后，应用自动删除所创建的输入存储容器，并允许你选择是否删除 Batch 池和作业。 BatchClient 的 [JobOperations](/python/api/azure.batch.operations.joboperations) 和 [PoolOperations](/python/api/azure.batch.operations.pooloperations) 类都有删除方法（在确认删除时调用）。 虽然作业和任务本身不收费，但计算节点收费。 因此，建议只在需要的时候分配池。 删除池时会删除节点上的所有任务输出。 但是，输入和输出文件保留在存储帐户中。

若不再需要资源组、Batch 帐户和存储帐户，请将其删除。 为此，请在 Azure 门户中选择 Batch 帐户所在的资源组，然后单击“删除资源组”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 通过 Batch 和存储帐户进行身份验证
> * 将输入文件上传到存储
> * 创建运行应用程序所需的计算节点池
> * 创建用于处理输入文件的作业和任务
> * 监视任务执行情况
> * 检索输出文件

如需更多示例，以便了解如何使用 Python API 来计划和处理 Batch 工作负荷，请参阅 GitHub 上的示例。

> [!div class="nextstepaction"]
> [Batch Python 示例](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

