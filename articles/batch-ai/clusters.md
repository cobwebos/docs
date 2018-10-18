---
title: 使用 Azure Batch AI 群集 | Microsoft Docs
description: 如何选择 Batch AI 群集配置，以及创建和管理群集 AI
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 61294d8b6b84b03b1e0c8d79b4d2855452c7f0e6
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44056706"
---
# <a name="work-with-batch-ai-clusters"></a>使用 Batch AI 群集 

本文介绍如何使用 Azure Batch AI 中的群集。 其中介绍了群集的概念、可能的配置类型和示例。 本文中演示如何创建和管理群集的大部分示例都使用 Azure CLI。 但是，也可以通过其他工具（包括 Azure 门户和 Azure Batch AI SDK）来使用群集。

Batch AI 群集是服务中的多种资源之一。 请参阅 [Batch AI 资源概述](resource-concepts.md)，了解服务中群集的范围。

## <a name="introduction-to-clusters"></a>群集简介

Batch AI 中的群集包含用于运行机器学习和 AI 训练作业的计算资源。 群集中的所有节点具有相同的 VM 大小和 OS 映像。 Batch AI 提供多个选项用于创建可根据不同需求进行自定义的群集。 一般情况下，我们会针对完成某个项目所需的每种处理能力类别设置不同的群集。 可以根据需求和预算纵向扩展和缩减群集中的节点数。 可以预配并在整个团队中共享群集，个人可以各自预配自己的群集。 

每个群集位于名为“工作区”的 Batch AI 资源之下。 在预配任何群集之前，必须设置一个 Batch AI 工作区。 例如，如果使用的是 Azure CLI，请使用 [az batchai workspace create](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) 命令来设置工作区。 

创建群集后，可以逐个地将作业提交到队列中。 然后，Batch AI 将处理资源分配过程，以便在群集上运行作业。 

## <a name="cluster-configuration-options"></a>群集配置选项

规划群集时，请先确定计算要求。 Batch AI 提供灵活的配置选项，让你根据需求定制群集。 下面是预配群集时要考虑的主要选项：

* **VM 大小** - 选择在群集节点[支持的区域](https://azure.microsoft.com/global-infrastructure/services/)中可用的任意 [VM 大小](../virtual-machines/linux/sizes.md)。 每个群集只能包含一种 VM 大小，因此，如果任务需要多种类型的 VM，则你需要针对每种类型的计算要求单独预配一个群集。 若要训练在利用 GPU 的框架中开发的机器学习或 AI 模型，请参阅 Azure 中的 [GPU 优化 VM 大小](../virtual-machines/linux/sizes-gpu.md)。
  
* **VM 优先级** - Batch AI 为群集提供专用或低优先级 VM。 专用 VM 是在群集中使用的预留 VM。 低优先级选项可分配未使用的 Azure VM 容量，这样可以大大降低成本，但代价是，如果 Azure 回收 VM，则作业可能会预先清空。 在低优先级 VM 上运行 24 小时以上的作业也会自动预先清空。 如果预算是个问题，请考虑对短时间运行的试验作业使用低优先级 VM。 在需要长时间运行作业时，再切换到专用 VM。

* **节点数** - Batch AI 针对群集中的节点数提供手动和自动缩放选项。 使用手动选项可以控制何时要纵向扩展和缩减群集，以便可以管理自己的计算成本。 自动缩放选项可以确保在不使用群集时始终将其缩减，以尽量减少计算成本。 

  如果选择手动缩放群集，则需要在创建群集期间定义初始目标。 目标是 Batch AI 最初分配的节点数。 以后，可以手动调整节点数。  

  如果选择自动缩放选项，则需要在创建群集期间定义目标节点数上限和下限。 目标范围可为 0 到 [Batch AI 核心配额](quota-limits.md)支持的节点数上限。 如果目标为 0，则无需支付任何计算费用即可维持群集配置。 如果请求的目标高于配额限制支持的值，则预配将会失败。 

* **VM 映像** - 默认情况下，Batch AI 使用支持容器工作负荷的默认 Ubuntu 服务器映像预配群集 VM。 可以选择 Azure 市场中提供的其他预配置 Linux 映像，例如 [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md)。 

* **存储** - Batch AI 提供自动存储选项，使用 Azure CLI 创建群集时可以选择此选项。 此选项在新的存储帐户下自动创建一个 Azure 文件共享和 Blob 容器。 在执行时，这些存储资源将装载到群集中的每个节点，因而可以从本地路径访问文件。 存储帐户名称、文件共享名称、Blob 容器名称和装载路径都具有默认值，在创建群集期间，也可以使用附加的参数来自定义这些属性。 

  如果未定义任何存储选项，则需要单独创建存储资源，并在提交作业时定义这些资源。 如果群集在组织级别进行管理，但存储在用户级别进行管理，则此选项非常有用。 有关如何将文件上传到 Azure 存储以及在执行时访问这些文件的详细信息，请参阅[使用 Azure 存储来存储 Batch AI 作业输入和输出](use-azure-storage.md)。

* **用户访问** - Batch AI 允许在创建群集时生成 SSH 公钥和私钥文件，或者提供你自己的 SSH 密钥，以便通过 SSH 连接到单个节点。 你还可以定义用户名（默认设置为当前用户）和密码。 在执行期间，可以使用这些凭据来访问节点，以查看各项指标或深入洞察作业。

* **设置任务** - Batch AI 允许定义分配时要在每个节点上执行的命令行参数。 你还可以定义输出文件要记录到的路径。 如果附加的预配步骤不仅仅涉及到基础映像，请使用此选项。

* **其他配置** - 有几种不太常见的方案可能需要更专业化的配置。 在这种情况下，可以使用 Azure CLI 命令附加[群集配置文件](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file)来创建群集。 

## <a name="create-the-cluster"></a>创建群集

确定群集配置选项后，使用 Azure CLI、Azure 门户或 Batch AI API 创建群集。 例如，若要使用 Azure CLI 创建群集，可以遵循 [az batchai cluster create](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) 文档创建确切的命令来获取所需的配置。 

以下命令使用最基本的配置预配包含一个节点并支持 SSH 访问的 Standard_NC6 群集。 默认情况下，此群集包含运行最新 Ubuntu 服务器映像的专用 VM。

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

以下示例预配一个可从 0 个节点自动扩展到 4 个节点，并使用低优先级节点和自动存储帐户的 Standard_NC6 群集。 如果需要低成本且易于管理的群集，则此设置是很合适的配置。 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

以下示例预配一个 Standard_NC6 群集，其中包含 Data Science Virtual Machine 映像、自定义存储和装载选项、自定义 SSH 凭据、可安装 *unzip*  包的安装任务，以及用于进行附加设置的群集配置文件。 此配置是可根据自身需求进一步进行自定义的群集示例。

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>监视群集

可以使用 [az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list)、[az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show) 和 [az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) 命令监视每个群集的各种信息。

### <a name="list-all-clusters"></a>列出所有群集

以下命令列出工作区中的所有群集。

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>显示有关群集的信息

以下命令以表格格式显示有关特定群集的完整信息。

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

如果群集是使用自动存储选项预配的，则你可以在上传脚本和训练作业时检索要使用的存储帐户名称。 请使用以下命令：

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

输出应如下所示。

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>列出群集节点

如果需要连接到群集节点，可使用以下命令来检索节点列表和连接信息。  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

针对每个节点的输出如下所示：

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
可运行如下所示的命令，使用此信息来与节点建立 SSH 连接。

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>将作业提交到群集

预配群集后，可以提交要在节点上运行的作业。 参阅 [az batchai job](/cli/azure/batchai/job?view=azure-cli-latest)命令，了解使用 Azure CLI 准备、提交和监视作业的不同方法。 

## <a name="downscale-cluster-for-later-use"></a>缩减群集供日后使用

运行完作业后，可以缩减群集。 不再使用群集时，我们建议始终缩减群集以节省计算成本。 将群集缩减为 0 个节点可以停止计费，同时，将来在需要再次扩展时无需重新预配群集。 如果在创建群集时选择了自动缩放，则群集应会自动缩减到最小目标。 如果选择了手动缩放，可使用以下命令缩减群集。

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>删除群集

使用完群集后，使用 [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) 命令将其删除。

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

删除资源组也会自动删除在该资源组下预配的所有群集。

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>后续步骤

有关创建 Batch AI 群集的更多示例，请参阅[门户](quickstart-create-cluster-portal.md)或 [Azure CLI](quickstart-create-cluster-cli.md) 快速入门，或 GitHub 上的 [Batch AI 诀窍](https://github.com/Azure/BatchAI/tree/master/recipes)。
