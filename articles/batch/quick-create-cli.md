---
title: Azure 快速入门 - 运行 Batch 作业 - CLI
description: 快速了解如何使用 Azure CLI 运行 Batch 作业。 从命令行或通过脚本创建和管理 Azure 资源。
ms.topic: quickstart
ms.date: 07/03/2018
ms.custom: mvc
ms.openlocfilehash: 3ec3ab6cc988ba1d11231a1bda1eec15d6e811c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116326"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>快速入门：使用 Azure CLI 运行第一个 Batch 作业

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门介绍如何使用 Azure CLI 创建 Batch 帐户、包含计算节点（虚拟机）的*池*以及在池中运行*任务*的*作业*。 每个示例任务在一个池节点上运行一个基本命令。 完成本快速入门以后，你会了解 Batch 服务的重要概念，并可使用更逼真的工作负荷进行更大规模的 Batch 试用。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.20 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus2”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>创建存储帐户

可以将 Azure 存储帐户与 Batch 帐户相关联。 虽然本快速入门不需要，但存储帐户可以用于为大多数现实世界的工作负荷部署应用程序和存储输入和输出数据。 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令在资源组中创建存储帐户。

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>创建批处理帐户

使用 [az batch account create](/cli/azure/batch/account#az-batch-account-create) 命令创建 Batch 帐户。 需要一个帐户来创建计算资源（计算节点池）和 Batch 作业。

以下示例在 *myResourceGroup* 中创建名为 *mybatchaccount* 的 Batch 帐户，并链接已创建的存储帐户。  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

若要创建和管理计算池和作业，需使用 Batch 进行身份验证。 使用 [az batch account login](/cli/azure/batch/account#az-batch-account-login) 命令登录到帐户。 登录后，`az batch` 命令使用此帐户上下文。

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>创建计算节点池

有了 Batch 帐户以后，即可使用 [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) 命令创建包含 Linux 计算节点的示例池。 以下示例创建名为 *mypool* 的池，其中包含 2 个大小为 *Standard_A1_v2* 且运行 Ubuntu 16.04 LTS 的节点。 就此快速示例来说，建议的节点大小在性能和成本之间达成了很好的平衡。
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

Batch 会立即创建池，但分配和启动计算节点则需要数分钟。 在此期间，池处于`resizing`状态。 若要查看池的状态，请运行 [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) 命令。 此命令显示池的所有属性，你可以查询特定的属性。 以下命令获取池的分配状态：

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

继续以下步骤，在池状态更改的情况下创建作业和任务。 如果分配状态为`steady`且所有节点处于运行状态，则说明池已做好运行任务的准备。 

## <a name="create-a-job"></a>创建作业

有了池之后，请创建可在其中运行的作业。  Batch 作业是适用于一个或多个任务的逻辑组。 作业包含任务的公用设置，例如优先级以及运行任务的池。 使用 [az batch job create](/cli/azure/batch/job#az-batch-job-create) 命令创建 Batch 作业。 以下示例在 *mypool* 池中创建 *myjob* 作业。 作业一开始没有任务。

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>创建任务

现在，请使用 [az batch task create](/cli/azure/batch/task#az-batch-task-create) 命令创建一些在作业中运行的任务。 此示例创建四个相同的任务。 每个任务都运行`command-line`，用于显示计算节点上的 Batch 环境变量，然后等待 90 秒。 使用 Batch 时，可以在此命令行中指定应用或脚本。 Batch 提供多种将应用和脚本部署到计算节点的方式。

以下 Bash 脚本创建 4 个并行任务（*mytask1* 到 *mytask4*）。

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

命令输出显示每个任务的设置。 Batch 将任务分发到计算节点。

## <a name="view-task-status"></a>查看任务状态

创建任务后，Batch 会让其排队在池中运行。 可以使用节点来运行该任务后，任务就会运行。

使用 [az batch task show](/cli/azure/batch/task#az-batch-task-show) 命令查看 Batch 任务的状态。 以下示例显示在一个池节点上运行的 *mytask1* 的详细信息。

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

命令输出包括许多详细信息，但请记下任务命令行的 `exitCode` 以及 `nodeId`。 `exitCode` 为 0 指示任务命令行已成功完成。 `nodeId` 指示任务运行时所在池节点的 ID。

## <a name="view-task-output"></a>查看任务输出

若要列出计算节点上的任务所创建的文件，请使用 [az batch task file list](/cli/azure/batch/task) 命令。 以下命令列出 *mytask1* 创建的文件： 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

输出与下面类似：

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

若要将某个输出文件下载到本地目录，请使用 [az batch task file download](/cli/azure/batch/task) 命令。 在此示例中，任务输出位于 `stdout.txt`。 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

可以在文本编辑器中查看 `stdout.txt` 的内容。 内容显示在节点上设置的 Azure Batch 环境变量。 创建自己的 Batch 作业时，可以在任务命令行中引用这些环境变量，并在命令行运行的应用和脚本中引用它们。 例如：

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>清理资源
若要继续学习 Batch 教程和示例，请使用在本快速入门中创建的 Batch 帐户和关联的存储帐户。 Batch 帐户本身不收费。

只要有节点在运行，就会对池收费，即使没有计划作业。 不再需要池时，请使用 [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete) 命令将其删除。 删除池时会删除节点上的所有任务输出。 

```azurecli-interactive
az batch pool delete --pool-id mypool
```

如果不再需要资源组、Batch 帐户、池和所有相关的资源，则可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。 删除资源，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门创建了 Batch 帐户、Batch 池和 Batch 作业。 作业运行示例任务，你查看了在其中一个节点上产生的输出。 了解 Batch 服务的重要概念以后，即可使用更逼真的工作负荷进行更大规模的 Batch 试用。 若要详细了解 Azure Batch，请继续学习 Azure Batch 教程。 


> [!div class="nextstepaction"]
> [Azure Batch 教程](./tutorial-parallel-dotnet.md)
