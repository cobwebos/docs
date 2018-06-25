---
title: Azure Batch 池的 Azure 文件共享 | Microsoft Docs
description: 如何在 Azure Batch 中从 Linux 或 Windows 池中的计算节点装载 Azure 文件共享。
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 88d7c0d033d7b517a396df27468de8be7ae20be9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811762"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>将 Batch 池与 Azure 文件共享配合使用

[Azure 文件](../storage/files/storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过服务器消息块 (SMB) 协议进行访问。 本文提供有关在池计算节点上装载和使用 Azure 文件共享的信息与代码示例。 代码示例使用 Batch .NET 和 Python SDK，但你可以使用其他 Batch SDK 和工具执行类似的操作。

Batch 原生支持通过 API 使用 Azure 存储 Blob 来读取和写入数据。 但是，在某些情况下，你可能想要从池计算节点访问 Azure 文件共享。 例如，某个旧式工作负荷依赖于 SMB 文件共享，或者任务需要访问共享数据或生成共享输出。 

## <a name="considerations-for-use-with-batch"></a>与 Batch 配合使用时的注意事项

* 如果池中运行的并行任务数相对较少，请考虑使用 Azure 文件共享。 请查看[性能和可伸缩性目标](../storage/files/storage-files-scale-targets.md)，确定在指定了预期的池大小和资产文件数的情况下，是否应使用 Azure 文件（Azure 文件使用 Azure 存储帐户）。 

* Azure 文件共享非常[经济高效](https://azure.microsoft.com/pricing/details/storage/files/)，可以使用数据复制将其配置到另一个区域，以实现全局冗余。 

* 可从本地计算机并行装载 Azure 文件共享。

* 另请参阅 Azure 文件共享的一般[规划注意事项](../storage/files/storage-files-planning.md)。


## <a name="create-a-file-share"></a>创建文件共享

在已链接到 Batch 帐户的存储帐户中，或者在单独的存储帐户中[创建文件共享](../storage/files/storage-how-to-create-file-share.md)。

## <a name="mount-a-share-on-a-windows-pool"></a>在 Windows 池中装载共享

本部分提供在 Windows 节点池中装载和使用 Azure 文件共享的步骤与代码示例。 有关其他背景信息，请参阅有关在 Windows 上装载 Azure 文件共享的[文档](../storage/files/storage-how-to-use-files-windows.md)。 

在 Batch 中，每次在 Windows 节点上运行任务时，都需要装载共享。 目前，在 Windows 节点上完成每个任务后无法保留网络连接。

例如，包含 `net use` 命令，以将文件共享装载为每个任务命令行的一部分。 若要装载文件共享，需要以下凭据：

* **用户名**：AZURE\\\<存储帐户名称\>，例如 AZURE\\*mystorageaccountname*
* **密码**：<以 == 结尾的存储帐户密钥>，例如 *XXXXXXXXXXXXXXXXXXXXX==*

以下命令在存储帐户 *mystorageaccountname* 中将文件共享 *myfileshare* 装载为 *S:* 驱动器：

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

为简单起见，此处的示例直接以文本形式传递凭据。 在实践中，我们强烈建议使用环境变量、证书或 Azure Key Vault 等解决方案来管理凭据。

为了简化装载操作，请选择性地在节点上保留凭据。 然后，无需凭据即可装载共享。 执行以下两个步骤：

1. 使用池配置中的启动任务运行 `cmdkey` 命令行实用工具。 这会在每个 Windows 节点上保留凭据。 启动任务命令行类似于：

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. 使用 `net use` 将每个节点上的共享装载为每个任务的一部分。 例如，以下任务命令行将文件共享装载为 *S:* 驱动器。 此命令行后接一个引用共享的命令或脚本。 在对 `net use` 的调用中使用缓存的凭据。 此步骤假设对任务使用的用户标识与在池的启动任务中使用的标识相同，并非所有场景都适合这样做。

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>C# 示例
以下 C# 示例演示如何使用启动任务在 Windows 池中保留凭据。 存储文件服务名称和存储凭据作为定义的常量传递。 此处，启动任务在具有池范围的标准（非管理员）自动用户帐户下运行。

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

存储凭据后，使用任务命令行来装载共享，并在读取或写入操作中引用共享。 作为基本示例，以下代码片段中的任务命令行使用 `dir` 命令列出文件共享中的文件。 请确保使用在池中运行启动任务时所用的相同[用户标识](batch-user-accounts.md)来运行每个作业任务。 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>在 Linux 池中装载共享

可以使用 [CIFS 内核客户端](https://wiki.samba.org/index.php/LinuxCIFS)在 Linux 分发版中装载 Azure 文件共享。 以下示例演示如何在 Ubuntu 16.04 LTS 计算节点池中装载文件共享。 如果使用不同的 Linux 分发版，则一般步骤与此相似，但需要使用适用于该分发版的包管理器。 有关详细信息和其他示例，请参阅[在 Linux 中使用 Azure 文件](../storage/files/storage-how-to-use-files-linux.md)。

首先，以管理员用户标识安装 `cifs-utils` 包，并在本地文件系统中创建装入点（例如 */mnt/MyAzureFileShare*）。 可以在文件系统上的任何位置创建装入点的文件夹，但是通用约定是在 `/mnt` 文件夹下创建此文件夹。 请务必不要直接在 `/mnt`（适用于 Ubuntu）或 `/mnt/resource`（适用于其他分发版）中创建装入点。

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

然后，运行 `mount` 命令并提供以下凭据来装载文件共享：

* **用户名**：\<存储帐户名称\>，例如 *mystorageaccountname*
* **密码**：<以 == 结尾的存储帐户密钥>，例如 *XXXXXXXXXXXXXXXXXXXXX==*

以下命令在存储帐户 *mystorageaccountname* 中的 */mnt/MyAzureFileShare* 装载文件共享 *myfileshare*： 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

为简单起见，此处的示例直接以文本形式传递凭据。 在实践中，我们强烈建议使用环境变量、证书或 Azure Key Vault 等解决方案来管理凭据。

在 Linux 池中，可在单个启动任务中合并所有这些步骤，或者在脚本中运行这些步骤。 在池中以管理员用户身份运行启动任务。 将启动任务设置为等待成功完成，然后再在池中运行引用共享的其他任务。

### <a name="python-example"></a>Python 示例

以下 Python 示例演示如何将 Ubuntu 池配置为在启动任务中装载共享。 装入点、文件共享终结点和存储凭据作为定义的常量传递。 启动任务在具有池范围的管理员自动用户帐户下运行。

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

装载共享并定义作业之后，在任务命令行中使用共享。 例如，以下基本命令使用 `ls` 列出文件共享中的文件。

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>后续步骤

* 有关 Batch 中用于读取和写入数据的其他选项，请参阅 [Batch 功能概述](batch-api-basics.md)及[保存作业和任务的输出](batch-task-output.md)。

* 另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 工具包，其中包含用于部署 Batch 容器工作负荷文件系统的 [Shipyard 脚本](https://github.com/Azure/batch-shipyard/tree/master/recipes)。