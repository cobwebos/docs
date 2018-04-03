---
title: Azure 快速入门 - 使用 Batch AI 进行 CNTK 训练 - Azure CLI | Microsoft Docs
description: 快速了解如何通过 Azure CLI 使用 Batch AI 运行 CNTK 训练作业
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>通过 Azure CLI 运行 CNTK 训练作业

本快速入门详述如何使用 Azure 命令行界面 (CLI) 通过 Batch AI 服务来运行 Microsoft Cognitive Toolkit (CNTK) 训练作业。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。

在此示例中，请使用包含手绘图像的 MNIST 数据库在由 Batch AI 管理的单节点 GPU 群集上训练一个卷积神经网络 (CNN)。 

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本快速入门需要运行最新的 Azure CLI 版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

还需使用 Azure Cloud Shell 或 Azure CLI 针对订阅注册 Batch AI 资源提供程序一次。 提供程序注册最多可能需要 15 分钟。

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>创建资源组

Batch AI 群集和作业是 Azure 资源，必须放置在 Azure 资源组中。

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。 然后，它会使用 [az configure](/cli/azure/reference-index#az_configure) 命令将此资源组和位置设置为默认。

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>为 `az` 命令设置默认值是一项可选步骤。 可以选择不设置默认值。 如果选择设置默认值，则应在完成教程后删除默认设置。 使用以下命令删除默认设置：
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>创建存储帐户

本快速入门使用 Azure 存储帐户托管训练作业的数据和脚本。 使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令创建存储帐户。

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>每个存储帐户都必须有唯一名称。 在以前的 `az` 命令中以及本教程的其他类似命令中，请将 `mystorageaccount` 设置的值替换为存储帐户名称。

## <a name="prepare-azure-file-share"></a>准备 Azure 文件共享

为了进行演示，本快速入门使用 Azure 文件共享托管学习作业的训练数据和脚本。

1. 使用 [az storage share create](/cli/azure/storage/share#az_storage_share_create) 命令创建名为 *batchaiquickstart* 的文件共享。

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. 使用 [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) 命令在名为 *mnistcntksample* 的共享中创建目录。

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. 下载[示例包](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D)并将其解压缩。 使用 [az storage file upload](/cli/azure/storage/file#az_storage_file_upload) 命令将内容上传到目录：

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>创建 GPU 群集
使用 [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) 命令创建一个 Batch AI 群集，其中包含单个 GPU VM 节点。 在此示例中，VM 运行默认的 Ubuntu LTS 映像。 改为指定 `image UbuntuDSVM` 来运行 Microsoft 深度学习虚拟机，后者支持更多的训练框架。 NC6 大小有一个 NVIDIA K80 GPU。 在名为 *azurefileshare* 的文件夹中装载文件共享。 此文件夹在 GPU 计算节点上的完整路径为 $AZ_BATCHAI_MOUNT_ROOT/azurefileshare。


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


创建群集后，输出如下所示：

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>获取群集状态

若要获取群集状态的概览，请运行 [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list) 命令：

```azurecli
az batchai cluster list -o table
```

输出与下面类似：

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

如需更多详细信息，请运行 [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show) 命令。 该命令返回在创建群集以后显示的所有群集属性。

当节点处于已分配状态且完成准备以后，群集会处于就绪状态（参见 `nodeStateCounts` 属性）。 如果出错，`errors` 属性会包含错误说明。

## <a name="create-training-job"></a>创建训练作业

群集准备就绪之后，请配置并提交学习作业。

1. 创建名为 job.json 的 JSON 模板文件，用于创建作业：

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. 使用 [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create) 命令创建可以在群集上运行的名为 *myjob* 的作业：

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

输出与下面类似：

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>监视作业

使用 [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) 命令获取作业状态的概览：

```azurecli
az batchai job list -o table
```

输出与下面类似：

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

如需更多详细信息，请运行 [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show) 命令。

`executionState` 包含作业的当前执行状态：

* `queued`：作业正等待群集节点变得可用
* `running`：作业正在运行
*   `succeeded`（或 `failed`）：作业已完成，`executionInfo` 包含有关结果的详细信息


## <a name="list-stdout-and-stderr-output"></a>列出 stdout 和 stderr 输出
使用 [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) 命令列出 stdout 和 stderr 日志文件的链接：

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

输出与下面类似：

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>观察输出

可以在作业正在执行时流式处理或跟踪作业的输出文件。 以下示例使用 [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) 命令来流式处理 stderr.txt 日志：

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

输出如下所示。 按 [Ctrl]-[C] 中断输出。

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>删除资源

使用 [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) 命令删除作业：

```azurecli
az batchai job delete --name myjob
```
使用 [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) 命令删除群集：

```azurecli
az batchai cluster delete --name mycluster
```

使用 `az group delete` 命令删除为本快速入门创建的资源组：

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>还原 Azure CLI 2.0 默认设置

删除此前为位置和资源组配置的默认设置：

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure CLI 在 Batch AI 群集上运行 CNTK 训练作业。 若要详细了解如何通过不同的工具包来使用 Batch AI，请参阅[训练诀窍](https://github.com/Azure/BatchAI)。

若要详细了解如何使用 Azure CLI 2.0 来管理 Batch AI，请参阅 [GitHub 文档](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md)。
