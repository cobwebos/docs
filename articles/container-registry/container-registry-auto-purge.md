---
title: 清除标记和清单
description: 使用 "清除" 命令可根据 age 和标记筛选器从 Azure 容器注册表中删除多个标记和清单，并选择性地计划清除操作。
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: 0ec1f5f6f5c3c572b8558c971b58e46cce36e3fd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923108"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>自动清除 Azure 容器注册表中的映像

使用 Azure 容器注册表作为开发工作流的一部分时，注册表会迅速填满一小段时间后不需要的图像或其他项目。 你可能想要删除早于某一持续时间或与指定名称筛选器匹配的所有标记。 若要快速删除多个项目，本文介绍 `acr purge` 命令，你可以按需或[计划](container-registry-tasks-scheduled.md)的 ACR 任务来运行该命令。 

`acr purge` 命令当前分布于公共容器映像（`mcr.microsoft.com/acr/acr-cli:0.1`）中，该映像是从 GitHub 中的[acr-cli](https://github.com/Azure/acr-cli)存储库中的源代码生成的。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装来运行本文中的 ACR 任务示例。 如果要在本地使用，则需要版本2.0.69 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。 

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 使用 `acr purge` 命令时要小心--删除的映像数据不可恢复。 如果系统通过清单摘要（而不是映像名称）提取映像，则不应清除未标记的图像。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 请考虑采用*唯一的标记*方案（[建议的最佳做法](container-registry-image-tag-version.md)），而不是按清单进行请求。

如果要使用 Azure CLI 命令删除单个映像标记或清单，请参阅[在 Azure 容器注册表中删除容器映像](container-registry-delete.md)。

## <a name="use-the-purge-command"></a>使用 "清除" 命令

`acr purge` 容器命令按标记在与名称筛选器匹配且早于指定持续时间的存储库中删除图像。 默认情况下，仅删除标记引用，而不删除基础[清单](container-registry-concepts.md#manifest)和层数据。 命令还可以选择删除清单。 

> [!NOTE]
> `acr purge` 不会删除 `write-enabled` 属性设置为 `false`的图像标记或存储库。 有关信息，请参阅[在 Azure 容器注册表中锁定容器映像](container-registry-image-lock.md)。

`acr purge` 旨在作为[ACR 任务](container-registry-tasks-overview.md)中的容器命令运行，使其能够在运行任务的注册表中自动进行身份验证，并在其中执行操作。 本文中的任务示例使用 `acr purge` 命令[别名](container-registry-tasks-reference-yaml.md#aliases)来代替完全限定的容器映像命令。

至少在运行 `acr purge`时指定以下内容：

* `--filter`-用于筛选存储库中的标记的存储库和*正则表达式*。 示例： `--filter "hello-world:.*"` 匹配 `hello-world` 存储库中的所有标记，并且 `--filter "hello-world:^1.*"` 与以 `1`开头的标记匹配。 传递多个 `--filter` 参数以清除多个存储库。
* `--ago`-用于指示要在其上删除图像的持续时间的 "离开样式[持续时间" 字符串](https://golang.org/pkg/time/)。 持续时间由一个或多个十进制数字的序列组成，每个数字包含一个单位后缀。 有效的时间单位包括 "d" 表示天，"h" 表示小时，"m" 表示分钟。 例如，`--ago 2d3h6m` 选择上次修改时间超过2天、3小时和6分钟前的所有筛选过的映像，`--ago 1.5h` 选择上次修改时间超过1.5 小时之前的映像。

`acr purge` 支持几个可选参数。 本文的示例中使用了以下两项：

* `--untagged`-指定删除没有关联标记（未标记*清单*）的清单。
* `--dry-run`-指定不删除任何数据，但输出与运行无此标志的命令相同。 此参数可用于测试清除命令，以确保它不会无意中删除您要保留的数据。

对于其他参数，请运行 `acr purge --help`。 

`acr purge` 支持 ACR 任务命令的其他功能，包括流式处理的[运行变量](container-registry-tasks-reference-yaml.md#run-variables)和[任务运行日志](container-registry-tasks-overview.md#view-task-logs)，还保存用于以后检索。

### <a name="run-in-an-on-demand-task"></a>在按需任务中运行

下面的示例使用[az acr run][az-acr-run]命令按需运行 `acr purge` 命令。 此示例将删除*myregistry*中已在1天前修改的 `hello-world` 存储库中的所有图像标记和清单。 使用环境变量传递容器命令。 任务在没有源上下文的情况下运行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>在计划任务中运行

下面的示例使用[az acr task create][az-acr-task-create]命令创建每日计划的[acr 任务](container-registry-tasks-scheduled.md)。 该任务将清除在 `hello-world` 存储库中超过7天前修改的标记。 使用环境变量传递容器命令。 任务在没有源上下文的情况下运行。

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

运行[az acr task show][az-acr-task-show]命令以查看计时器触发器是否已配置。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>清除大量标记和清单

清除大量标记和清单可能需要几分钟或更长时间。 若要清除数千个标记和清单，请求任务的命令可能需要比默认超时时间长600秒，对于计划任务，此命令可能需要3600秒。 如果超过超时时间，则仅删除标记和清单的子集。 若要确保大规模清除已完成，请传递 `--timeout` 参数以增加值。 

例如，以下按需任务会将超时时间设置为3600秒（1小时）：

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>示例：在注册表中按计划清除多个存储库

此示例演示如何使用 `acr purge` 在注册表中定期清除多个存储库。 例如，你可能有一个开发管道，它将图像推送到 `samples/devimage1` 并 `samples/devimage2` 存储库。 你将开发映像定期导入到部署的生产存储库中，这样你就不再需要开发映像了。 每周都将清除 `samples/devimage1` 和 `samples/devimage2` 存储库，以便为未来一周的工作做准备。

### <a name="preview-the-purge"></a>预览清除

在删除数据之前，我们建议使用 `--dry-run` 参数运行按需清除任务。 此选项可让你查看命令将清除的标记和清单，而无需删除任何数据。 

在下面的示例中，每个存储库中的筛选器都选择所有标记。 `--ago 0d` 参数匹配存储库中与筛选器匹配的所有年龄段的图像。 根据方案需要修改选择条件。 `--untagged` 参数指示除了删除标记之外还删除清单。 使用环境变量将容器命令传递到[az acr run][az-acr-run]命令。

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

查看命令输出，查看与选择参数匹配的标记和清单。 由于命令是 `--dry-run`运行的，因此不会删除任何数据。

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

### <a name="schedule-the-purge"></a>计划清除

验证完干布后，创建一个计划任务以自动执行清除。 下面的示例将周任务计划于星期日 1:00 UTC，以运行以前的清除命令：

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

运行[az acr task show][az-acr-task-show]命令以查看计时器触发器是否已配置。

## <a name="next-steps"></a>后续步骤

了解其他用于删除 Azure 容器注册表中的[图像数据](container-registry-delete.md)的选项。

有关映像存储的详细信息，请参阅[Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

