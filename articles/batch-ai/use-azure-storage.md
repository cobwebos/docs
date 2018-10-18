---
title: 使用 Azure 存储来存储 Batch AI 作业输入和输出 | Microsoft Docs
description: 如何将 Azure 存储与 Batch AI 结合使用，以便轻松快速地在云端存储输入和输出文件
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a2b6b3334176cb6fdd86c17b4d11cb03a42dd4bf
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731796"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>使用 Azure 存储来存储 Batch AI 作业输入和输出

本指南介绍如何在运行作业时使用 Azure 存储来存储输入和输出文件。 Azure 存储是受 Batch AI 支持的几种存储选项之一。 Batch AI 通过将 Azure 存储系统装载到 Batch AI 作业或群集文件系统来与 Azure 存储集成，从而允许无缝访问存储在云中的文件。 

## <a name="introduction-to-azure-storage"></a>Azure 存储简介

Azure 存储是 Microsoft 提供的云存储解决方案。 Batch AI 支持将 Azure Blob 容器和 Azure 文件共享装载到 Batch AI 作业或群集，从而允许从作业访问文件，就像它们在本机文件系统中一样。 Batch AI 通过 [blobfuse](https://github.com/Azure/azure-storage-fuse) 装载 Azure Blob 容器，通过 SMB 协议装载 Azure 文件共享。 有关 Azure 存储的详细信息，请参阅 [Azure 存储简介](../storage/common/storage-introduction.md)。

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>在 Azure 存储中存储数据集和输入脚本

为 Batch AI 环境选择 Azure 存储时，我们建议将输入文件（例如数据集）存储在具有较高吞吐量的 Blob 容器中，将训练输出存储在支持流式传输的文件共享中（允许在作业并发运行时读取输出日志）。 

必须[创建 Azure 存储帐户](../storage/common/storage-quickstart-create-account.md)，才能使用 Azure 存储。 Batch AI 支持从常规用途 v1 (GPv1) 和常规用途 v2 (GPv2) Azure 存储帐户中装载卷。 Azure 存储帐户可以容纳多个 Blob 容器或文件共享实例。 选择要创建的存储帐户类型时，请考虑成本和性能要求。 有关详细信息，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)。 

若要创建 Blob 容器并将数据集上载到 Azure Blob 容器，请选择以下方法之一：
- [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md)，通过基于 Web 的 GUI 进行上载。 若要上载少量文件，Azure 门户可提供最简单的操作。
- [Azure 存储 CLI](../storage/blobs/storage-quickstart-blobs-cli.md)，通过命令行上载（支持目录上载）。 若要上载文件目录，请使用 `az storage blob upload-batch`。
- [其他方法](../storage/common/storage-moving-data.md)，包括使用应用程序 SDK。

同样，若要创建 Azure 文件共享，请选择以下方法之一：
- [Azure 门户](../storage/files/storage-how-to-use-files-portal.md)
- [Azure 存储 CLI](../storage/files/storage-how-to-use-files-cli.md)
- [其他方法](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>自动存储与 Batch AI

或者，可以结合使用 `--use-auto-storage` 参数和 `az batchai cluster create`，创建具有 Azure 文件共享和 Blob 容器的 Azure 存储帐户（并自动将这些卷装载到 Batch AI 群集）。 有关详细信息，请参阅[此文](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account)。

## <a name="mount-azure-storage"></a>装载 Azure 存储 

### <a name="mount-volumes-to-a-job"></a>将卷装载到作业

装载 Azure 存储卷后，可通过为每个作业创建的文件系统访问它。 因此，作业可以在云存储中无缝地读取和写入文件，就像它们是本地文件一样。

若要将 Azure 存储卷装载到使用 Azure CLI 创建的作业，请在运行 `az batchai job create` 时使用 `job.json` 文件中的 `mountVolumes` 属性。 有关示例，请参阅 [Tensorflow GPU 分布式方案](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json)。 `mountVolumes` 的架构如下：
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` 和 `azureBlobFileSystems` 都是表示要装载的卷的对象数组。 对占位符的描述：

- <RELATIVE_MOUNT_PATH>：卷将装载在此路径中。 例如，如果 `relativeMountPath` 为 `jobs`，则卷将位于 `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs` 中）
- <STORAGE_ACCOUNT_NAME>：包含文件共享或 Blob 容器的 Azure 存储帐户的名称
- <FILE_SHARE_NAME>：文件共享的名称
- <BLOB_CONTAINER_NAME>：Blob 容器的名称

若要使用 Azure Batch AI SDK 装载 Azure 存储卷，请在 `JobCreateParameters` 上设置 `mount_volumes` (Python) 或 `MountVolumes`（C#、Java）属性。 使用 Azure Batch AI SDK 装载卷时，必须提供存储帐户的凭据。 请查看在 [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python)、[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet) 和 [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable) 中装载卷的架构。

### <a name="mount-volumes-to-a-cluster"></a>将卷装载到群集

Batch AI 还支持将 Azure 存储卷装载到 Batch AI 群集。 将卷装载到群集时，该群集上运行的所有作业都可以使用装载到该群集的卷。 虽然作业级装载可提供最大的灵活性（允许每个作业具有不同的已装载卷），但在简单的方案中，群集级装载可能就足够了。

若要将 Azure 存储卷装载到使用 Azure CLI 创建的群集，请在运行 `az batchai cluster create` 时使用 `cluster.json` 文件中的 `mountVolumes` 属性。 装载群集时 `mountVolumes` 的架构与装载到作业时的架构相同。 

同样，在使用 Azure Batch AI SDK 进行装载时，可以在 `ClusterCreateParameters` 上使用 `mount_volumes` (Python) 或 `MountVolumes`（C#、Java）属性。 

## <a name="access-mounted-filesystem-in-a-job"></a>访问作业中装载的文件系统

### <a name="azbatchaijobmountroot-environment-variable"></a>$AZ_BATCHAI_JOB_MOUNT_ROOT 环境变量

在作业的执行环境中，可以使用 `$AZ_BATCHAI_JOB_MOUNT_ROOT` 环境变量（如果使用作业级装载）访问包含已装载的存储系统的目录。 如果使用群集级装载，则此环境变量为 `$AZ_BATCHAI_MOUNT_ROOT`。 以下示例假定使用作业级装载。

若要提供数据在已装载的卷中的路径，请将环境变量 `$AZ_BATCHAI_JOB_MOUNT_ROOT` 与已装载的路径一起使用。 例如，如果将训练脚本 `train.py` 上载到装载于相对装载路径 `scripts` 中的 Azure 文件共享，则该文件将出现在 `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py` 中。

如果训练脚本需要知道路径，则应将其作为命令行参数传递。 例如，如果将数据存储在 Azure Blob 容器（装载于路径 `data` 中）中名为 `train_data` 的文件夹中，则可以将 `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` 作为命令行参数传递给脚本。 因此，必须修改脚本以接受命令行参数。

### <a name="abbreviate-input-paths"></a>缩写输入路径

若要将输入路径缩写为环境变量，请使用 `job.json` 文件的 `inputDirectories` 属性（如果使用 Batch AI SDK，则为 `models.JobCreateParamters.input_directories`）。 `inputDirectories` 的架构如下：

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

指定的每个路径都将放在一个名为 `$AZ_BATCHAI_INPUT_<ID>` 的环境变量中。 使用此方法可以简化输入文件或目录的路径。 例如，缩写训练脚本的路径：如果 `"id"` 为 `"SCRIPT"`，`"path"` 为 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`，则该路径将出现在作业执行环境的 `$AZ_BATCHAI_INPUT_SCRIPT` 中。

有关详细信息，请参阅[此文](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories)。

### <a name="abbreviate-output-paths"></a>缩写输出路径

若要将输出路径缩写为环境变量，请使用 `job.json` 文件的 `outputDirectories` 属性（如果使用 Batch AI SDK，则为 `models.JobCreateParamters.output_directories`）。 使用此方法可以简化输出文件的路径。 `outputDirectories` 的架构如下：

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

指定的每个路径都将放在一个名为 `$AZ_BATCHAI_OUTPUT_<ID>` 的环境变量中。 `pathPrefix` 确定用于存储输出的已装载卷（例如，`"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`）。 `pathSuffix` 确定输出的文件夹名称（例如，`"logs"`、`"checkpoints"`）。 输出的实际路径为 `pathPrefix`、`jobOutputDirectoryPathSegment`（为每个作业自动生成）和 `pathSuffix` 的串联。

有关详细信息，请参阅[此文](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories)。

## <a name="retrieve-job-output-from-azure-storage"></a>从 Azure 存储中检索作业输出

### <a name="use-azure-portal"></a>使用 Azure 门户

Azure 门户是一种使用 GUI 文件资源管理器查看作业输出的便捷方式。 但是，如果要从 Stdout 和 Stderr 或从 `outputDirectories` 中的路径查看输出，则文件将放置在 Azure 存储卷的自动生成路径中。 有关详细信息，请参阅下文。

### <a name="access-stdout-and-stderr-output"></a>访问 Stdout 和 Stderr 输出

使用 `job.json` 的 `stdOutErrPathPrefix` 属性指示作业将作业的执行日志和 Stdout 和 Stderr 输出放在何处。 例如，如果在相对装载路径 `outputs` 中装载了文件共享，并且将 `stdOutErrPathPrefix` 指定为 `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`，那么 Stdout 和 Stderr 作业输出将出现在该已装载卷的 `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` 中。 此自动生成的路径用于防止同名作业之间发生输出冲突。

有关详细信息，请参阅[此文](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output)。

### <a name="list-the-output-files"></a>列出输出文件

可以使用 Azure CLI 通过 `az batchai job file list` 命令列出作业的可用输出文件。 例如，若要列出作业的标准输出目录中的文件，请使用 `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`。

有关更多信息和示例，请参阅[此处](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)。

### <a name="stream-output-files"></a>流式传输输出文件

可以使用 Azure CLI 通过 `az batchai job file stream` 命令流式传输文件。 例如，查看：
- Stdout：`az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr：`az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

有关更多信息和示例，请参阅[此处](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories)。

## <a name="next-steps"></a>后续步骤
- 若要详细了解用于与 Azure 存储交互的 CLI 命令，请查看 [Azure Batch AI CLI 文档](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md)。
- 若要查找 Batch AI 的更多用法示例，包括装载存储和读取输出文件，请参阅 [Batch AI 的 Jupyter Notebook 方案](https://github.com/Azure/BatchAI)。
- 浏览有关装载存储的其他选项，包括[装载 NFS 服务器](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)和[装载你自己的 NFS、cifs 或 GlusterFS 群集](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)