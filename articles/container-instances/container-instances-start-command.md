---
title: 替代容器实例中的入口点
description: 部署 Azure 容器实例时，设置一个命令行来替代容器映像中的入口点
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169623"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>在容器实例中设置命令行来替代默认的命令行操作

创建容器实例时，可以指定一个命令来替代已植入到容器映像中的默认命令行指令。 此行为类似于在 `--entrypoint` 命令行中指定参数 `docker run`。

与为容器实例设置[环境变量](container-instances-environment-variables.md)一样，对于需要使用特定于任务的配置动态准备每个容器的批处理作业，指定启动命令行非常有用。

## <a name="command-line-guidelines"></a>命令行准则

* 默认情况下，命令行在容器中指定*不通过 shell 启动的单个进程*。 例如，命令行可能运行某个 Python 脚本或可执行文件。 该进程可以指定其他参数或自变量。

* 若要执行多个命令，请通过设置容器操作系统中支持的 shell 环境来开始命令行。 示例:

  |操作系统  |默认 shell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  遵循 shell 的约定，组合多个命令以按顺序运行。

* 根据容器配置，你可能需要设置命令行可执行文件或自变量的完整路径。

* 为容器实例设置适当的[重启策略](container-instances-restart-policy.md)，具体取决于命令行指定的是长时间运行的任务还是运行一次运行的任务。 例如，对于只运行一次的任务，建议使用 `Never` 或 `OnFailure` 重启策略。 

* 如果需要容器映像中设置的默认入口点的相关信息，请使用 [docker 映像检查](https://docs.docker.com/engine/reference/commandline/image_inspect/)命令。

## <a name="command-line-syntax"></a>命令行语法

命令行语法根据用于创建实例的 Azure API 或工具而有所不同。 如果指定某个 shell 环境，还应遵守该 shell 的命令语法约定。

* [az container create][az-container-create] 命令：通过 `--command-line` 参数传递一个字符串。 示例：`--command-line "python myscript.py arg1 arg2"`）。

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet：通过 `-Command` 参数传递一个字符串。 示例：`-Command "echo hello"`。

* Azure 门户：在容器配置的“命令替代”  属性中，提供一个逗号分隔的字符串（不带引号）。 示例：`python, myscript.py, arg1, arg2`）。 

* 资源管理器模板或 YAML 文件或 Azure SDK 之一：将命令行属性指定为字符串数组。 示例：资源管理器模板中的 JSON 数组 `["python", "myscript.py", "arg1", "arg2"]`。 

  如果你熟悉 [Dockerfile](https://docs.docker.com/engine/reference/builder/) 语法，此格式类似于 CMD 指令的 *exec* 形式。

### <a name="examples"></a>示例

|    |  Azure CLI   | 门户 | 模板 | 
| ---- | ---- | --- | --- |
| **单个命令** | `--command-line "python myscript.py arg1 arg2"` | **命令替代**：`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **多个命令** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令替代**：`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 示例

例如，修改 [microsoft/aci-wordcount][aci-wordcount] 容器映像的行为，该映像分析莎士比亚的《哈姆雷特》  中的文本来查找最常出现的单词。 你可以设置一个指向不同文本源的命令行，而不是分析《哈姆雷特》  。

若要查看 [microsoft/aci-wordcount][aci-wordcount] 容器在分析默认文本时的输出，请使用以下 [az container create][az-container-create] 命令来运行它。 未指定任何启动命令行，因此将运行默认容器命令。 为了进行说明，此示例设置了[环境变量](container-instances-environment-variables.md)来查找长度至少为五个字母且排名前 3 的单词：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

在容器的状态显示为 *Terminated* 后（使用 [az container show][az-container-show] 来检查状态），通过 [az container logs][az-container-logs] 显示日志来查看输出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

输出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

现在，通过指定一个不同的命令行来分析不同文本。 容器执行的 Python 脚本 *wordcount.py* 接受一个 URL 作为参数，并处理该页面的内容而不是默认内容。

例如，若要确定《罗密欧和朱丽叶》  中长度至少为五个字母且排名前 3 的单词，请使用以下命令：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

同样，容器状态显示为 *Terminated* 后，可通过显示容器的日志来查看输出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

输出：

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>后续步骤

基于任务的方案（例如对使用多个容器的数据库进行批处理）可以在运行时充分利用自定义命令行。 若要详细了解如何运行基于任务的容器，请参阅[使用重启策略运行容器化任务](container-instances-restart-policy.md)。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
