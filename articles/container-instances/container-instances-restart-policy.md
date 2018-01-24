---
title: "在 Azure 容器实例中运行容器化任务"
description: "了解如何使用 Azure 容器实例来执行一直要运行到完成的任务，例如生成、测试渲染作业或制作其映像。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: a922525970eac9af6657e58daae971912183b369
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>在 Azure 容器实例中运行容器化任务

在 Azure 容器实例中部署容器的简便性和速度，使该服务成了可用于执行一次性任务（例如，在容器实例中生成、测试渲染作业并制作其映像）的引人注目的平台。

使用可配置的重启策略，可将容器指定为在完成其进程后停止。 由于容器实例按秒计费，我们只需针对执行任务的容器在运行时所用的计算资源付费。

本文演示的示例使用 Azure CLI。 必须在[本地安装][azure-cli-install] Azure CLI 2.0.21 或更高版本，或使用 [Azure Cloud Shell](../cloud-shell/overview.md) 中的 CLI。

## <a name="container-restart-policy"></a>容器重启策略

在 Azure 容器实例中创建容器时，可以指定三个重启策略设置中的一个。

| 重启策略   | 说明 |
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

要查看重启策略的工作方式，请基于 [microsoft/aci-wordcount][aci-wordcount-image] 映像创建一个容器实例，并指定 `OnFailure` 重启策略。 此示例容器运行一个 Python 脚本，默认情况下，该脚本会分析莎士比亚著作[哈姆雷特](http://shakespeare.mit.edu/hamlet/full.html)中的文本，将 10 个最常见的单词写入 STDOUT，然后退出。

使用以下 [az container create][az-container-create] 命令运行示例容器：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
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

## <a name="configure-containers-at-runtime"></a>在运行时配置容器

创建容器实例时，可设置其**环境变量**，并指定一个要在启动容器时执行的自定义**命令行**。 可以在批处理作业中使用这些设置，以使用特定于任务的配置准备每个容器。

## <a name="environment-variables"></a>环境变量

在容器中设置环境变量，以提供容器运行的应用程序或脚本的动态配置。 这类似于在 `--env` 命令行中指定参数 `docker run`。

例如，创建容器实例时，可以通过指定以下环境变量来修改示例容器中脚本的行为：

*NumWords*：发送到 STDOUT 的单词数。

*MinLength*：单词中最少包含几个字符才将它统计为一个单词。 如果指定较大的数字，将会忽略“of”和“the”等常见单词。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

对容器的环境变量指定 `NumWords=5` 和 `MinLength=8` 后，容器日志应会显示不同的输出。 容器状态显示为 *Terminated*（使用 `az container show` 可检查其状态）后，将显示其日志用于查看新的输出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

输出：

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>命令行重写

创建容器实例时，可以指定一个命令行用于重写容器映像中植入的命令行。 这类似于在 `--entrypoint` 命令行中指定参数 `docker run`。

例如，可以通过指定不同的命令行，让示例容器分析除“哈姆雷特”以外的文本。 容器执行的 Python 脚本 *wordcount.py* 接受使用 URL 作为参数，并处理该页面的内容而不是默认内容。

例如，若要确定“罗密欧与朱丽叶”中前 3 个由五个字母构成的单词，请运行以下命令：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

同样，容器状态显示为 *Terminated* 后，可通过显示容器的日志来查看输出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

输出：

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>后续步骤

### <a name="persist-task-output"></a>保留任务输出

有关如何保存一直运行到完成的容器的输出，请参阅[装载包含 Azure 容器实例的 Azure 文件共享](container-instances-mounting-azure-files-volume.md)。

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
