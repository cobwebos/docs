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
ms.openlocfilehash: 8d2381f710e87751bd6547c7f435080f185020d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608539"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>通过 Azure CLI 运行 CNTK 训练作业

Azure CLI 2.0 用于创建和管理 Batch AI 资源 - 创建/删除 Batch AI 文件服务器和群集，以及提交/终止/删除/监视训练作业。

本快速入门介绍如何使用 Microsoft Cognitive Toolkit 创建 GPU 群集并运行训练作业。

训练脚本 [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) 在 Batch AI GitHub 页面提供。 此脚本在存储手写数字的 MNIST 数据库的基础上训练卷积神经网络。

正式的 CNTK 示例已经过修改，可以通过命令行参数接受训练数据集位置和输出目录位置。

## <a name="quickstart-overview"></a>快速入门概述

* 创建名为 `nc6` 的单节点 GPU 群集（VM 大小为 `Standard_NC6`）；
* 创建新的用于存储作业输入和输出的存储帐户；
* 创建一个 Azure 文件共享，使用两个名为 `logs` 和 `scripts` 的文件夹，分别存储作业输出和训练脚本；
* 创建用于存储训练数据的 Azure Blob 容器 `data`；
* 将训练脚本和训练数据部署到创建的文件共享和容器；
* 配置作业，以便将 Azure 文件共享和 Azure Blob 容器装载到群集的节点上，使之可以作为常规文件系统在 `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`、`$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` 和 `$AZ_BATCHAI_JOB_MOUNT_ROOT/data` 中提供。
`AZ_BATCHAI_JOB_MOUNT_ROOT` 是一个由作业的 Batch AI 设置的环境变量。
* 监视作业执行情况，方法是流式处理其标准输出；
* 在作业完成以后，检查其输出和生成的模型；
* 最后，删除所有已分配的资源。

# <a name="prerequisites"></a>先决条件

* Azure 订阅 - 如果没有 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure CLI 2.0（版本为 2.0.31 或以上）的访问权限。 可以使用 [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) 中提供的 Azure CLI 2.0，也可以按照[这些说明](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)在本地进行安装。

# <a name="cloud-shell-only"></a>仅 Cloud Shell

如果使用 Cloud Shell，请将工作目录更改为 `/usr/$USER/clouddrive`，因为主目录没有空的空间：

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>创建资源组。

Azure 资源组是用于部署和管理 Azure 资源的逻辑容器。 以下命令在“美国东部”位置创建新资源组 ```batchai.quickstart```：

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>创建 GPU 群集

以下命令创建单节点 GPU 群集（VM 大小为 Standard_NC6），使用 Ubuntu DSVM 作为操作系统映像：

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Ubuntu DSVM 用于在 docker 容器中运行任何训练作业，以及直接在 VM 上运行最常用的深度学习框架。

`--generate-ssh-keys` 选项告知 Azure CLI 生成专用和公用 ssh 密钥（如果还没有）。 可以使用当前的用户名和生成的 ssh 密钥访问群集节点。

注意，如果使用 Cloud Shell，请将 ~/.ssh 文件夹备份到某个永久存储。

示例输出：
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>创建存储帐户

以下命令在 batchai.repices 资源组所在的区域创建新的存储帐户。 请使用唯一的存储帐户名称更新命令。

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

如果所选存储帐户名称不可用，则以上命令会报告相应的错误。 在这种情况下，请选择其他名称，然后重试。

# <a name="data-deployment"></a>数据部署

## <a name="download-the-training-script-and-training-data"></a>下载训练脚本和训练数据

* 从[此位置](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D)下载预处理的 MNIST 数据库并将其提取到当前文件夹。

对于 GNU/Linux 或 Cloud Shell：

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

注意，如果 GNU/Linux 发行版没有 `unzip`，则可能需要安装它。

* 将 [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) 示例脚本下载到当前文件夹中：

对于 GNU/Linux 或 Cloud Shell：

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>创建 Azure 文件共享并部署训练脚本

以下命令创建 Azure 文件共享 `scripts` 和 `logs` 并将训练脚本复制到 `scripts` 共享中的 `cntk` 文件夹：

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>创建 Blob 容器并部署训练数据

以下命令创建 Azure Blob 容器 `data` 并将训练数据复制到 `mnist_cntk` 文件夹中：
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>提交训练作业

## <a name="prepare-job-configuration-file"></a>准备作业配置文件

创建一个内容如下的训练作业配置文件 `job.json`：
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

此配置文件指定：

* `nodeCount` - 作业所需的节点数（1 个用于本快速入门）；
* `cntkSettings` - 指定训练脚本的路径和命令行参数。 命令行参数包括训练数据的路径以及用于存储所生成模型的目标路径。 `AZ_BATCHAI_OUTPUT_MODEL` 是一个由 Batch AI 根据输出目录配置（见下）设置的环境变量；
* `stdOutErrPathPrefix` - 一个路径，Batch AI 会在其中创建包含作业输出和日志的目录；
* `outputDirectories` - 将要由 Batch AI 创建的输出目录的集合。 Batch AI 为每个目录创建名为 `AZ_BATCHAI_OUTPUT_<id>` 的环境变量，其中的 `<id>` 为目录标识符；
* `mountVolumes` - 要在作业执行过程中装载的文件系统的列表。 这些文件系统装载到 `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>` 下。 `AZ_BATCHAI_JOB_MOUNT_ROOT` 是一个由 Batch AI 设置的环境变量；
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` 表示将在作业提交过程中通过 --storage-account-name 参数或 `AZURE_BATCHAI_STORAGE_ACCOUNT` 环境变量在计算机上指定存储帐户名称。

## <a name="submit-the-job"></a>提交作业

请使用以下命令在群集上提交作业：

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

示例输出：
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>监视作业执行情况

训练脚本在标准输出目录的 `stderr.txt` 文件中报告训练进度。 可使用以下命令监视进度：

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

示例输出：
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

流式处理在作业完成后（不管是成功还是失败）停止。

# <a name="inspect-generated-model-files"></a>检查生成的模型文件

作业将生成的模型文件存储在 `id` 属性为 `MODEL` 的输出目录中。可使用以下命令列出模型文件并获取下载 URL：

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

示例输出：
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

也可使用门户或 Azure 存储资源管理器检查生成的文件。 为了区分不同作业的输出，Batch AI 为每个作业创建唯一的文件夹结构。 可以使用已提交作业的 `jobOutputDirectoryPathSegment` 属性查找包含输出的文件夹的路径：

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

示例输出：
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>删除资源

请使用以下命令删除资源组和所有分配的资源：

```azurecli
az group delete -n batchai.quickstart -y
```
