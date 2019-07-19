---
title: 在 Azure 容器实例中使用启动命令行
description: 部署 Azure 容器实例时, 重写容器映像中配置的入口点
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 99440e22eb736522a25c2ee56bb07ef1d9967e66
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325666"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>在容器实例中设置命令行以覆盖默认的命令行操作

创建容器实例时, 可以选择指定命令以将默认命令行指令融入重写到容器映像中。 此行为类似于的`--entrypoint`命令行`docker run`参数。

与为容器实例设置[环境变量](container-instances-environment-variables.md)一样, 指定启动命令行对于需要使用特定于任务的配置动态准备每个容器的批处理作业非常有用。

## <a name="command-line-guidelines"></a>命令行准则

* 默认情况下, 命令行指定一个*进程, 该进程在容器中不带 shell 的情况下启动*。 例如, 命令行可能运行 Python 脚本或可执行文件。 

* 若要执行多个命令, 请通过设置容器操作系统中支持的 shell 环境来开始命令行。 示例：

  |操作系统  |默认 shell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  遵循 shell 的约定, 组合多个命令以便按顺序运行。

* 根据容器配置, 可能需要设置命令行可执行文件的完整路径或自变量。

* 为容器实例设置适当的[重启策略](container-instances-restart-policy.md), 具体取决于命令行指定长时间运行的任务还是运行一次运行的任务。 例如, 对于只运行一次`Never`的`OnFailure`任务, 建议使用或的重新启动策略。 

* 如果需要有关容器映像中的默认入口点集的信息, 请使用[docker image 检查](https://docs.docker.com/engine/reference/commandline/image_inspect/)命令。

## <a name="command-line-syntax"></a>命令行语法

命令行语法根据用于创建实例的 Azure API 或工具而有所不同。 如果指定 shell 环境, 还应遵循 shell 的命令语法约定。

* [az container create][az-container-create]命令:传递包含`--command-line`参数的字符串。 示例: `--command-line "python myscript.py arg1 arg2"`)。

* [新-get-azurermcontainergroup][new-azurermcontainergroup]Azure PowerShell cmdlet:传递包含`-Command`参数的字符串。 示例：`-Command "echo hello"`。

* Azure 门户：在容器配置的**命令重写**属性中, 提供一个逗号分隔的字符串列表 (不带引号)。 示例: `python, myscript.py, arg1, arg2`)。 

* 资源管理器模板或 YAML 文件或 Azure Sdk 之一:将命令行属性指定为字符串数组。 示例: 资源管理器模板中`["python", "myscript.py", "arg1", "arg2"]`的 JSON 数组。 

  如果你熟悉[Dockerfile](https://docs.docker.com/engine/reference/builder/)语法, 此格式类似于 cmd.exe 指令的*exec*形式。

### <a name="examples"></a>示例

|    |  Azure CLI   | 门户 | 模板 | 
| ---- | ---- | --- | --- |
| 单个命令 | `--command-line "python myscript.py arg1 arg2"` | **命令替代**:`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 多个命令 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令替代**:`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 示例

例如, 修改[microsoft/wordcount][aci-wordcount]容器映像的行为, 该映像分析莎士比亚的*哈姆雷特*中的文本, 以查找最常出现的单词。 您可以设置一个指向不同文本源的命令行, 而不是分析*哈姆雷特*。

查看[microsoft/wordcount][aci-wordcount] container when it analyzes the default text, run it with the following [az container create][az-container-create]命令的输出。 未指定任何启动命令行, 因此默认容器命令将运行。 出于说明目的, 此示例将设置[环境变量](container-instances-environment-variables.md)以查找长度至少为五个字符的前3个单词:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

容器状态显示为已*终止*后 (使用[az container show][az-container-show] to check state), display the log with [az container logs][az-container-logs]查看输出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

输出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

现在, 通过指定其他命令行来设置第二个示例容器来分析不同文本。 由容器*wordcount.py*执行的 Python 脚本接受 URL 作为参数, 并处理该页面的内容而不是默认值。

例如, 若要在*罗密欧和朱丽叶*中确定长度至少为五个字符的前3个单词:

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

基于任务的方案 (如批处理包含多个容器的大型数据集) 可在运行时从自定义命令行受益。 有关运行基于任务的容器的详细信息, 请参阅[运行包含重新启动策略的容器化任务](container-instances-restart-policy.md)。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
