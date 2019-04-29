---
title: 对 Azure 容器实例中的容器化任务使用重启策略
description: 了解如何使用 Azure 容器实例来执行一直要运行到完成的任务，例如生成、测试渲染作业或制作其映像。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608116"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>使用重启策略运行容器化任务

在 Azure 容器实例中部署容器的简便性和速度，使该服务成了可用于执行一次性任务（例如，在容器实例中生成、测试渲染作业并制作其映像）的引人注目的平台。

使用可配置的重启策略，可将容器指定为在完成其进程后停止。 由于容器实例按秒计费，我们只需针对执行任务的容器在运行时所用的计算资源付费。

本文演示的示例使用 Azure CLI。 必须在[本地安装][azure-cli-install] Azure CLI 2.0.21 或更高版本，或使用 [Azure Cloud Shell](../cloud-shell/overview.md) 中的 CLI。

## <a name="container-restart-policy"></a>容器重启策略

在 Azure 容器实例中创建[容器组](container-instances-container-groups.md)时，可以指定三个重启策略设置中的一个。

| 重启策略   | 描述 |
| ---------------- | :---------- |
| `Always` | 始终重启容器组中的容器。 如果在创建容器时未指定重启策略，则会应用此**默认**设置。 |
| `Never` | 永远不重启容器组中的容器。 容器最多运行一次。 |
| `OnFailure` | 仅当容器中执行的进程失败（终止且返回非零退出代码）时，才重启容器组中的容器。 容器至少运行一次。 |

## <a name="specify-a-restart-policy"></a>指定重启策略

重启策略的指定方式取决于容器实例的创建方式，例如，是使用 Azure CLI、Azure PowerShell cmdlet 还是 Azure 门户。 在 Azure CLI 中，请在调用 [az container create][az-container-create] 时指定 `--restart-policy` 参数。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>一直运行到完成的示例

若要查看操作中的重启策略，可从 Microsoft 创建的容器实例[aci wordcount] [ aci-wordcount-image]映像，并指定`OnFailure`重启策略。 此示例容器运行一个 Python 脚本，默认情况下，该脚本会分析莎士比亚著作[哈姆雷特](http://shakespeare.mit.edu/hamlet/full.html)中的文本，将 10 个最常见的单词写入 STDOUT，然后退出。

使用以下 [az container create][az-container-create] 命令运行示例容器：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure 容器实例将启动该容器，然后在其应用程序（在本例中为脚本）退出时停止。 当 Azure 容器实例停止重启策略为 `Never` 或 `OnFailure` 的某个容器时，该容器的状态将设置为 **Terminated**。 可以使用 [az container show][az-container-show] 命令检查容器的状态：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

示例输出：

```bash
"Terminated"
```

当示例容器的状态显示为 *Terminated* 后，可以通过查看容器日志来查看该容器的任务输出。 运行 [az container logs][az-container-logs] 命令可查看脚本的输出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

输出：

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

此示例显示了由脚本发送到 STDOUT 的输出。 但是，容器化任务可能会将其输出写入到持久性存储供以后检索。 例如，写入到 [Azure 文件共享](container-instances-mounting-azure-files-volume.md)。

## <a name="next-steps"></a>后续步骤

基于任务的情况下，例如批处理具有多个容器的大型数据集可以充分利用自定义[环境变量](container-instances-environment-variables.md)或[命令行](container-instances-start-command.md)在运行时。

有关如何保存一直运行到完成的容器的输出，请参阅[装载包含 Azure 容器实例的 Azure 文件共享](container-instances-mounting-azure-files-volume.md)。

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
