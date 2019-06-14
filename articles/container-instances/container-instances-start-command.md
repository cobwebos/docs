---
title: 在 Azure 容器实例中使用正在启动的命令行
description: 重写时部署 Azure 容器实例容器映像中配置的入口点
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569635"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>若要重写默认命令行操作的容器实例中设置命令行

创建容器实例时，可以选择指定的命令重写到容器映像提供支持的默认命令行指令。 此行为是类似于`--entrypoint`命令行参数`docker run`。

像设置[环境变量](container-instances-environment-variables.md)容器实例的指定起始的命令行可用于批处理作业需要准备使用特定于任务的配置动态每个容器。

## <a name="command-line-guidelines"></a>命令行指导原则

* 默认情况下，指定命令行*单一而无需 shell 启动进程*容器中。 例如，命令行可能会运行 Python 脚本或可执行文件。 

* 若要执行多个命令，请先通过设置容器操作系统中受支持的 shell 环境命令行。 示例：

  |操作系统  |默认 shell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  按照命令行程序将合并多个命令，在序列中运行的约定。

* 具体取决于容器配置，可能需要设置命令行可执行文件的完整路径或参数。

* 设置适当[重启策略](container-instances-restart-policy.md)对于容器实例，具体取决于是否在命令行指定一个长时间运行的任务或运行一次任务。 例如，重启策略的`Never`或`OnFailure`建议用于运行一次任务。 

* 如果你需要在容器映像中设置的默认入口点有关的信息，使用[docker 映像检查](https://docs.docker.com/engine/reference/commandline/image_inspect/)命令。

## <a name="command-line-syntax"></a>命令行语法

命令行语法各不相同，具体取决于 Azure API 或工具来创建实例。 如果指定 shell 环境，还发现在 shell 的命令语法约定。

* [az 容器创建][ az-container-create]命令：传递具有字符串`--command-line`参数。 示例： `--command-line "python myscript.py arg1 arg2"`)。

* [新 AzureRmContainerGroup] [ new-azurermcontainergroup] Azure PowerShell cmdlet:传递具有字符串`-Command`参数。 示例：`-Command "echo hello"`。

* Azure 门户：在中**命令重写**容器配置的属性提供的不带引号的字符串，以逗号分隔列表。 示例： `python, myscript.py, arg1, arg2`)。 

* 资源管理器模板或 YAML 文件，或 Azure Sdk 之一：命令行属性指定为一个字符串数组。 示例： JSON 数组`["python", "myscript.py", "arg1", "arg2"]`Resource Manager 模板中。 

  如果您熟悉[Dockerfile](https://docs.docker.com/engine/reference/builder/)语法，此格式是类似于*exec* CMD 指令的窗体。

### <a name="examples"></a>示例

|    |  Azure CLI   | 门户 | 模板 | 
| ---- | ---- | --- | --- |
| 单个命令 | `--command-line "python myscript.py arg1 arg2"` | **命令重写**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 多个命令 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令重写**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 示例

例如，修改的行为[microsoft/aci wordcount] [ aci-wordcount]容器映像，会分析莎士比亚著作中的文本*哈姆雷特*若要查找的最大频率出现的单词。 而不是分析*哈姆雷特*，可以设置指向不同的文本源的命令行。

若要查看的输出[microsoft/aci wordcount] [ aci-wordcount]时，它将分析默认的文本，运行以下[az 容器创建][az-container-create]命令。 指定没有启动命令行，因此，默认容器命令运行。 出于演示目的，此示例设置[环境变量](container-instances-environment-variables.md)查找至少五个字母长的前 3 个字：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

容器的状态显示为*Terminated* (使用[az 容器显示][ az-container-show]检查状态)，显示具有日志[az 容器日志] [ az-container-logs]以查看输出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

输出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

现在设置第二个示例容器来通过指定不同的命令行分析不同的文本。 执行容器的 Python 脚本*wordcount.py*、 接受 URL 作为参数，并处理该页面的内容，而不是默认值。

例如，若要确定顶部都至少 5 位的 3 个字的字母长数据类型位于*罗密欧和朱丽叶*:

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

基于任务的情况下，例如批处理具有多个容器的大型数据集可以受益于在运行时的自定义命令行。 有关正在运行的基于任务的容器的详细信息，请参阅[重启策略运行容器化的任务](container-instances-restart-policy.md)。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
