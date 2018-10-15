---
title: Azure 快速入门 - 创建 Batch AI 群集 - Azure CLI | Microsoft Docs
description: 快速入门 - 创建用于训练机器学习和 AI 模型的 Batch AI 群集 - Azure CLI
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
ms.openlocfilehash: 0d4ba7edfb22a6710222c854ceb2bf86284d2d77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057442"
---
# <a name="quickstart-create-a-cluster-for-batch-ai-training-jobs-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建用于 Batch AI 训练作业的群集

本快速入门演示如何使用 Azure CLI 创建 Batch AI 群集，以便将其用于训练 AI 和机器学习模型。 Batch AI 是一项托管的服务，可以让数据科学家和 AI 研究人员在 Azure 虚拟机群集上大规模训练 AI 和机器学习模型。

群集最初只有一个 GPU 节点。 完成本快速入门后，你会有一个可以纵向扩展的群集，可以用来训练模型。 使用 Batch AI、[Azure 机器学习](../machine-learning/service/overview-what-is-azure-ml.md)工具或 [Visual Studio Tools for AI](https://github.com/Microsoft/vs-tools-for-ai) 将训练作业提交到群集。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.38 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

本快速入门假定你在 Bash shell 中运行命令，不管是在 Cloud Shell 中还是在本地计算机上。

## <a name="create-a-resource-group"></a>创建资源组

使用 `az group create` 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus2”位置创建名为“myResourceGroup”的资源组。 请务必选择其中提供了 Batch AI 服务的一个位置（例如，美国东部 2）。

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

若要创建 Batch AI 群集，请使用 `az batchai cluster create` 命令。 以下示例创建一个具有以下属性的群集：

* 在 NC6 VM 大小中包含单个节点，该大小具有一个 NVIDIA Tesla K80 GPU。 
* 运行一个默认的 Ubuntu Server 映像，该映像旨在托管基于容器的应用程序，而此类应用程序可以用于大多数训练工作负荷。 
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

命令输出显示群集属性。 创建和启动节点需要几分钟的时间。 若要查看群集的状态，请运行 `az batchai cluster show` 命令。 

```azurecli-interactive
az batchai cluster show \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --output table
```

在群集创建过程的早期阶段，输出如下所示，表明群集处于 `resizing` 状态：

```bash
Name       Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
---------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
mycluster  myResourceGroup   myworkspace  STANDARD_NC6  resizing      0          0            0          0           0

```
如果状态为 `steady` 且单个节点为 `Idle` 状态，则群集就绪可运行。

## <a name="list-cluster-nodes"></a>列出群集节点 

如需连接到群集节点（在此示例中为单个节点）来安装应用程序或执行维护，请通过运行 `az batchai cluster node list` 命令来获取连接信息：


```azurecli-interactive
az batchai cluster node list \
    --cluster mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup 
 ```

JSON 输出类似于：

```JSON
[
  {
    "ipAddress": "40.68.254.143",
    "nodeId": "tvm-1816144089_1-20180626t233430z",
    "port": 50000.0
  }
]
```
根据此信息进行 SSH 连接，以便连接到节点。 例如，在以下命令中使用你的节点的正确 IP 地址来替换相应值：

```bash
ssh myusername@40.68.254.143 -p 50000
``` 
退出 SSH 会话以继续操作。

## <a name="resize-the-cluster"></a>重设群集大小

使用群集来运行训练作业时，你可能需要更多计算资源。 例如，若要为分布式训练作业将大小增大到 2 个节点，请运行 [batch ai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) 命令：

```azurecli-interactive
az batchai cluster resize \
    --name mycluster \
    --workspace myworkspace \
    --resource-group myResourceGroup \
    --target 2
```

重设群集大小需要数分钟。

## <a name="clean-up-resources"></a>清理资源

若要继续学习 Batch AI 教程和示例，请使用在本快速入门中创建的 Batch AI 工作区。 

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
    --resource-group myResourceGroup \
```

如果不再需要 Batch AI 资源的资源组，可以使用 `az group delete` 命令将其删除。 

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已学习了如何使用 Azure CLI 创建 Batch AI 群集。 若要了解如何使用 Batch AI 群集来训练模型，请转到介绍如何训练深度学习模型的快速入门。

> [!div class="nextstepaction"]
> [训练深度学习模型](./quickstart-tensorflow-training-cli.md)
