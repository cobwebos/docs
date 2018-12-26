---
title: Azure 快速入门 - 深度学习训练 - Azure CLI | Microsoft Docs
description: 快速入门 - 快速了解如何使用 Azure CLI 在单个 GPU 上通过 Batch AI 训练 TensorFlow 深度学习神经网络
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 09/03/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 979253021f5503295e0572759b510e074ceb1a6b
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408062"
---
# <a name="quickstart-train-a-deep-learning-model-with-batch-ai"></a>快速入门：通过 Batch AI 训练深度学习模型

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

本快速入门介绍如何在由 Batch AI 托管且支持 GPU 的虚拟机上训练示例性的深度学习模型。 Batch AI 是一项托管的服务，可以让数据科学家和 AI 研究人员在 Azure 虚拟机群集上大规模训练 AI 和机器学习模型。 

在此示例中，请使用 Azure CLI 设置 Batch AI，以便在包含手写数字的 [MNIST 数据库](http://yann.lecun.com/exdb/mnist/)上训练一个示例性的 [TensorFlow](https://www.tensorflow.org/) 神经网络。 完成本快速入门以后，你会了解使用 Batch AI 来训练 AI 或机器学习模型的重要概念，并准备以更大的规模尝试训练不同的模型。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.38 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

本快速入门假定你在 Bash shell 中运行命令，不管是在 Cloud Shell 中还是在本地计算机上。 如果已经完成[使用 Azure CLI 创建 Batch AI 群集](quickstart-create-cluster-cli.md)所需的快速入门，请跳过用于创建资源组和 Batch AI 群集的头两个步骤。

## <a name="create-a-resource-group"></a>创建资源组

使用 `az group create` 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus2”位置创建名为“myResourceGroup”的资源组。 确保选择“美国东部 2”位置，或者其他提供 Batch AI 服务的位置。 

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-batch-ai-cluster"></a>创建 Batch AI 群集

首先，使用 `az batchai workspace create` 命令创建 Batch AI 工作区。 需要一个工作区来组织 Batch AI 群集和其他资源。

```azurecli-interactive
az batchai workspace create \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

若要创建 Batch AI 群集，请使用 `az batchai cluster create` 命令。 以下示例创建一个具有以下属性的单节点群集：

* 请使用 NC6 VM 大小，该大小包含一个 NVIDIA Tesla K80 GPU。 Azure 提供多个 VM 大小，其中的 NVIDIA GPU 数各不相同。
* 请运行默认的 Ubuntu Server 映像，该映像旨在托管基于容器的应用程序。 可以在此发行版上运行大多数训练工作负荷。 
* 添加名为 *myusername* 的用户帐户，并在本地环境中生成 SSH 密钥（如果这些密钥尚未存在于默认密钥位置 (*~/.ssh*) 中）。

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --vm-size Standard_NC6 \
    --target 1 \
    --user-name myusername \
    --generate-ssh-keys
```

命令输出显示群集属性。 创建和启动节点需要几分钟的时间。 若要查看群集状态，请运行 `az batchai cluster show` 命令。

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

在群集创建过程的早期阶段，输出如下所示，显示群集状态为`resizing`：

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```

在群集状态更改时，继续以下步骤，上传训练脚本并创建训练作业。 当群集状态为`steady`且单个节点处于`Idle`状态时，群集就可以运行训练作业。

## <a name="upload-training-script"></a>上传训练脚本

使用 `az storage account create` 命令创建一个存储帐户，以便存储训练脚本和训练输出。

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

使用 `az storage share create` 命令在帐户中创建名为 `myshare` 的 Azure 文件共享：

```azurecli-interactive
az storage share create \
    --name myshare \
    --account-name mystorageaccount
```

使用 `az storage directory create` 命令在 Azure 文件共享中创建目录。 为训练脚本创建 `scripts` 目录，为训练输出创建 `logs` 目录：

```azurecli-interactive
# Create /scripts directory in file share
az storage directory create \
    --name scripts \
    --share-name myshare \
    --account-name mystorageaccount

# Create /logs directory in file share 
az storage directory create \
    --name logs \
    --share-name myshare \
    --account-name mystorageaccount
```

在 Bash shell 中创建本地工作目录，并下载 TensorFlow [convolutional.py](https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py) 示例。 此 Python 脚本在存储 60,000 个手写数字（0 到 9）的 MNIST 图像集的基础上训练卷积神经网络。 然后，它会在一组测试示例上测试此模型。

```bash
wget https://raw.githubusercontent.com/tensorflow/models/master/tutorials/image/mnist/convolutional.py
```

使用 `az storage file upload` 命令将脚本上传到共享中的 `scripts` 目录。

```azurecli-interactive
az storage file upload \
    --share-name myshare \
    --path scripts \
    --source convolutional.py \
    --account-name mystorageaccount
```

## <a name="submit-training-job"></a>提交训练作业

首先，使用 `az batchai experiment create` 命令在工作区中创建 Batch AI 试验。 试验是相关 Batch AI 作业的逻辑容器。

```azurecli-interactive
az batchai experiment create \
    --name myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

在工作目录中，创建内容如下的训练作业配置文件 `job.json`。 在提交训练作业时传递此配置文件。

此 `job.json` 文件包含特定的设置，此类设置用于定位 Python 脚本文件并使其在 GPU 节点上的 TensorFlow 容器中运行。 它还指定一个位置，以便将作业的输出文件保存在 Azure 存储中的该位置。 `<AZURE_BATCHAI_STORAGE_ACCOUNT>` 指示将在作业提交过程中指定存储帐户名称。

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 1,
        "tensorFlowSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/scripts/convolutional.py"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/logs",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

请使用 `az batchai job create` 命令在节点上提交作业，传递 `job.json` 配置文件和存储帐户的名称：

```azurecli-interactive
az batchai job create \
    --name myjob \
    --cluster mycluster \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --config-file job.json \
    --storage-account-name mystorageaccount
```

在该命令返回作业属性后，请再等待数分钟，待其完成。 若要监视此作业的进度，请使用 `az batchai job file stream` 命令流式处理节点上标准输出目录中的 `stdout-wk-0.txt` 文件。 训练脚本在作业开始运行后生成此文件。  

```azurecli-interactive
az batchai job file stream \
    --job myjob \
    --experiment myexperiment \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --file-name stdout-wk-0.txt
```

示例输出：

```
File found with URL "https://mystorageaccount.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/myResourceGroup/workspaces/myworkspace/experiments/myexperiment/jobs/myjob/<JOB_ID>/stdouterr/stdout-wk-0.txt?sv=2016-05-31&sr=f&sig=Kih9baozMao8Ugos%2FVG%2BcsVsSeY1O%2FTocCNvLQhwtx4%3D&se=2018-06-20T22%3A07%3A30Z&sp=rl". Start streaming
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 14.9 ms
Minibatch loss: 8.334, learning rate: 0.010000
Minibatch error: 85.9%
Validation error: 84.6%
Step 100 (epoch 0.12), 9.7 ms
Minibatch loss: 3.240, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 7.7%
Step 200 (epoch 0.23), 8.3 ms
Minibatch loss: 3.335, learning rate: 0.010000
Minibatch error: 7.8%
Validation error: 4.5%
Step 300 (epoch 0.35), 8.3 ms
Minibatch loss: 3.157, learning rate: 0.010000
Minibatch error: 3.1%
...
Step 8500 (epoch 9.89), 8.3 ms
Minibatch loss: 1.605, learning rate: 0.006302
Minibatch error: 0.0%
Validation error: 0.9%
Test error: 0.8%
```

流式处理在作业完成后停止。 此示例脚本训练 10 个以上的时期，或者贯穿整个训练数据集。 在此示例中，在 10 个时期过后，训练的模型执行时的测试错误只占 0.8%。

## <a name="get-job-output"></a>获取作业输出

Batch AI 在存储帐户中为每个作业的输出创建唯一的文件夹结构。 使用此路径设置 JOB_OUTPUT_PATH 环境变量。 然后，使用 `az storage file list` 命令列出存储中的输出文件：

```azurecli-interactive
export JOB_OUTPUT_PATH=$(az batchai job show --name myjob --experiment myexperiment --workspace myworkspace --resource-group myResourceGroup --query jobOutputDirectoryPathSegment --output tsv)

az storage file list \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr \
    --output table
```

输出类似于：

```
Name               Content Length  Type    Last Modified
---------------  ----------------  ------  ---------------
execution.log               14866  file
stderr-wk-0.txt              1527  file
stdout-wk-0.txt             11027  file
```

使用 `az storage file download` 命令将一个或多个文件下载到本地工作目录。 例如：

```azurecli-interactive
az storage file download \
    --share-name myshare/logs \
    --account-name mystorageaccount \
    --path $JOB_OUTPUT_PATH/stdouterr/stdout-wk-0.txt
```

## <a name="clean-up-resources"></a>清理资源

若要继续学习 Batch AI 教程和示例，请使用在本快速入门中创建的 Batch AI 工作区、群集和存储帐户。 

有节点运行时，会对 Batch AI 群集收费。 若要在没有作业运行的情况下保留群集配置，请将群集大小重设为 0 个节点。 

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 0
```

随后请根据运行作业的需要，将其大小重设为 1 个或多个节点。 不再需要群集时，请使用 `az batchai cluster delete` 命令将其删除：

```azurecli-interactive
az batchai cluster delete \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup
```

如果不再需要 Batch AI 的资源组和存储资源，可以使用 `az group delete` 命令将其删除。 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何使用 Azure CLI 通过 Batch AI 在单个 GPU VM 上训练一个示例 TensorFlow 深度学习模型。 若要了解如何在更大的 GPU 群集上进行分布式模型训练，请继续学习 Batch AI 教程。

> [!div class="nextstepaction"]
> [分布式训练教程](./tutorial-horovod-tensorflow.md)