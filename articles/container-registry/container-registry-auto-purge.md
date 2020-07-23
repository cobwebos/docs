---
title: 清除标记和清单
description: 使用清除命令根据年龄和标记筛选器从 Azure 容器注册表中删除多个标记和清单，并选择性地安排清除操作计划。
ms.topic: article
ms.date: 05/14/2020
ms.openlocfilehash: ab6794648babd2bd491ded5788455b75c10d675a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652641"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>自动清除 Azure 容器注册表中的映像

在开发工作流中使用 Azure 容器注册表时，注册表中可能很快会填满在一小段时间后不需要的映像或其他项目。 你可能会想删除早于某一特定持续时间或与指定名称筛选器匹配的所有标记。 为帮助你快速删除多个项目，本文介绍了 `acr purge` 命令，可以将其作为按需或[计划的](container-registry-tasks-scheduled.md) ACR 任务来运行。 

`acr purge` 命令当前在公共容器映像 (`mcr.microsoft.com/acr/acr-cli:0.2`) 中分发，该映像通过 GitHub 中 [acr-cli](https://github.com/Azure/acr-cli) 存储库中的源代码生成。

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装来运行本文中的 ACR 任务示例。 如果想要在本地使用它，则需要使用 2.0.76 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。 

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 请谨慎使用 `acr purge` 命令，已删除映像数据是无法恢复的。 如果系统按清单摘要（而不是映像名称）拉取映像，则不应清除未标记的映像。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳做法](container-registry-image-tag-version.md)，即唯一标记方案。

如果想使用 Azure CLI 命令删除单个映像标记或清单，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

## <a name="use-the-purge-command"></a>使用清除命令

`acr purge` 容器命令按标记删除存储库中与名称筛选器匹配和早于指定持续时间的映像。 默认情况下，仅删除标记引用，而不会删除基础[清单](container-registry-concepts.md#manifest)和层数据。 该命令还提供删除清单的选项。 

> [!NOTE]
> `acr purge` 不会删除 `write-enabled` 属性设置为 `false` 的映像标记或存储库。 有关信息，请参阅[锁定 Azure 容器注册表中的容器映像](container-registry-image-lock.md)。

`acr purge` 旨在作为 [ACR 任务](container-registry-tasks-overview.md)中的容器命令运行，以便对运行任务的注册表自动进行身份验证，并在其中执行操作。 本文中的任务示例使用 `acr purge` 命令[别名](container-registry-tasks-reference-yaml.md#aliases)替代完全限定的容器映像命令。

运行 `acr purge` 时，请至少指定以下内容：

* `--filter` - 一个存储库和用于筛选存储库中标记的正则表达式。 示例：`--filter "hello-world:.*"` 匹配 `hello-world` 存储库中的所有标记，而 `--filter "hello-world:^1.*"` 匹配以 `1` 开头的标记。 传递多个 `--filter` 参数以清除多个存储库。
* `--ago` - 一个 Go 风格的[持续时间字符串](https://golang.org/pkg/time/)，用于指示超过多长时间即删除映像。 该持续时间由一个或多个十进制数字序列组成，每个数字都有一个单位后缀。 有效的时间单位包括“d”（表示天数）、“h”（表示小时数）和“m”（表示分钟数）。 例如，`--ago 2d3h6m` 选择上次修改时间早于 2 天 3 小时 6 分钟之前的所有已筛选的映像，`--ago 1.5h` 选择上次修改时间早于 1.5 小时之前的映像。

`acr purge` 支持几个可选参数。 本文中的示例使用了以下两个参数：

* `--untagged` - 指定删除没有相关标记的清单（未标记的清单）。
* `--dry-run` - 指定不删除任何数据，但其输出与在没有此标志的情况下运行此命令时的输出相同。 此参数有助于测试清除命令，确保它不会无意中删除要保留的数据。

对于其他参数，请运行 `acr purge --help`。 

`acr purge` 支持 ACR 任务命令的其他功能，包括经过流式处理并保存以供以后检索的[运行变量](container-registry-tasks-reference-yaml.md#run-variables)和[任务运行日志](container-registry-tasks-logs.md)。

### <a name="run-in-an-on-demand-task"></a>在按需任务中运行

以下示例使用 [az acr run][az-acr-run] 命令按需运行 `acr purge` 命令。 此示例将删除修改时间早于 1 天前的 myregistry 中的 `hello-world` 存储库中的所有映像标记和清单。 使用环境变量传递容器命令。 任务在没有源上下文的情况下运行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>在计划的任务中运行

以下示例使用 [az acr task create][az-acr-task-create] 命令创建每日运行的[计划的 ACR 任务](container-registry-tasks-scheduled.md)。 此任务清除 `hello-world` 存储库中早于 7 天前修改的标记。 使用环境变量传递容器命令。 任务在没有源上下文的情况下运行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

运行 [az acr task show][az-acr-task-show] 命令，查看是否已配置计时器触发器。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>清除大量标记和清单

清除大量标记和清单可能需要几分钟或更长时间。 若要清除数千个标记和清单，命令需要运行的时间可能会比按需任务的默认超时时间 600 秒或计划的任务的默认超时时间 3600 秒要长。 如果超过超时时间，则仅会删除部分标记和清单。 若要确保完成大规模清除操作，请传递 `--timeout` 参数以增加此值。 

例如，以下按需任务将超时时间设置为 3600 秒（1 小时）：

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>示例：计划清除注册表中的多个存储库

此示例演示如何使用 `acr purge` 定期清除注册表中的多个存储库。 例如，你可能有一个将映像推送到 `samples/devimage1` 和 `samples/devimage2` 存储库的开发管道。 你定期将开发映像导入到部署的生产存储库，因此不再需要开发映像。 你会每周清除 `samples/devimage1` 和 `samples/devimage2` 存储库，以便为未来一周的工作做好准备。

### <a name="preview-the-purge"></a>预览清除

在删除数据之前，我们建议使用 `--dry-run` 参数运行按需清除任务。 通过此选项，可以查看命令将清除的标记和清单，而不会删除任何数据。 

在以下示例中，每个存储库中的筛选器都会选择所有标记。 `--ago 0d` 参数与存储库中与筛选器匹配的所有年龄的映像匹配。 根据方案的需要修改选择条件。 `--untagged` 参数指示除了删除标记之外还要删除清单。 使用环境变量将容器命令传递到 [az acr run][az-acr-run] 命令。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

查看命令输出，以查看与选择参数匹配的标记和清单。 由于命令通过 `--dry-run` 运行，因此不会删除任何数据。

示例输出：

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>安排清除计划

验证 dry run 后，创建一个计划的任务以自动执行清除。 以下示例安排了一个在每周日 UTC 1:00 运行的每周任务，通过它运行之前的清除命令：

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

运行 [az acr task show][az-acr-task-show] 命令，查看是否已配置计时器触发器。

## <a name="next-steps"></a>后续步骤

了解用于在 Azure 容器注册表中[删除映像数据](container-registry-delete.md)的其他选项。

有关映像存储的详细信息，请参阅 [Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

