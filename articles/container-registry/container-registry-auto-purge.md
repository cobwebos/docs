---
title: 清除标记和清单
description: 使用清除命令根据年龄和标记筛选器从 Azure 容器注册表中删除多个标记和清单，并可以选择计划清除操作。
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087339"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>自动清除 Azure 容器注册表中的图像

将 Azure 容器注册表用作开发工作流的一部分时，注册表可以快速填充在短时间内不需要的图像或其他项目。 您可能希望删除早于特定持续时间的所有标记或匹配指定的名称筛选器。 为了快速删除多个项目，本文介绍了可以作为按需`acr purge`或[计划的](container-registry-tasks-scheduled.md)ACR 任务运行的命令。 

该`acr purge`命令当前分布在一个公共容器映像 （），`mcr.microsoft.com/acr/acr-cli:0.1`由 GitHub 中的[acr-cli](https://github.com/Azure/acr-cli)存储库中的源代码生成。

可以使用 Azure 云外壳或 Azure CLI 的本地安装来运行本文中的 ACR 任务示例。 如果您想在本地使用它，则需要版本 2.0.69 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。 

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 请谨慎`acr purge`使用该命令-删除的图像数据是"无法恢复"。 如果系统通过清单摘要（而不是图像名称）提取图像，则不应清除未标记的图像。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳实践，即唯一标记方案](container-registry-image-tag-version.md)**。

如果要使用 Azure CLI 命令删除单个图像标记或清单，请参阅[在 Azure 容器注册表中删除容器映像](container-registry-delete.md)。

## <a name="use-the-purge-command"></a>使用清除命令

`acr purge`容器命令通过与名称筛选器匹配且早于指定持续时间的存储库中的标记删除图像。 默认情况下，仅删除标记引用，而不是基础[清单](container-registry-concepts.md#manifest)和图层数据。 该命令可以选择也删除清单。 

> [!NOTE]
> `acr purge`不删除`write-enabled`将属性设置为`false`的图像标记或存储库。 有关详细信息，请参阅[在 Azure 容器注册表中锁定容器映像](container-registry-image-lock.md)。

`acr purge`设计为在[ACR 任务中](container-registry-tasks-overview.md)作为容器命令运行，以便它使用运行任务的注册表自动进行身份验证，并在那里执行操作。 本文中的任务示例使用`acr purge`命令[别名](container-registry-tasks-reference-yaml.md#aliases)代替完全限定的容器映像命令。

至少，在运行`acr purge`： 时指定以下内容：

* `--filter`- 用于筛选存储库中标记的存储库和*正则表达式*。 示例：`--filter "hello-world:.*"`匹配存储库中的所有`hello-world`标记，并`--filter "hello-world:^1.*"`匹配以 开头的`1`标记。 传递多个`--filter`参数以清除多个存储库。
* `--ago`- Go 样式[持续时间字符串](https://golang.org/pkg/time/)，用于指示删除图像的持续时间。 持续时间由一个或多个小数组成的序列组成，每个数字都有一个单元后缀。 有效时间单位包括"d"为数，小时为"h"，m"表示分钟数。 例如，`--ago 2d3h6m`选择上次修改超过 2 天、3 小时和 6 分钟之前的所有筛选图像，并`--ago 1.5h`选择上次修改超过 1.5 小时的图像。

`acr purge`支持多个可选参数。 本文中的示例中使用了以下两个示例：

* `--untagged`- 指定删除没有关联标记（*未标记的清单*）的清单。
* `--dry-run`- 指定不删除任何数据，但输出与在没有此标志的情况下运行命令相同。 此参数可用于测试清除命令，以确保它不会无意中删除要保留的数据。

对于其他参数，运行`acr purge --help`。 

`acr purge`支持 ACR Task 命令的其他功能，包括流式处理并保存以供以后检索[的运行变量](container-registry-tasks-reference-yaml.md#run-variables)和[任务运行日志](container-registry-tasks-logs.md)。

### <a name="run-in-an-on-demand-task"></a>在按需任务中运行

下面的示例使用[az acr run][az-acr-run]命令按需`acr purge`运行该命令。 本示例删除 1 天前修改的`hello-world`*myREGISTRY*存储库中的所有图像标记和清单。 容器命令使用环境变量传递。 任务在没有源上下文的情况下运行。

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

下面的示例使用[az acr 任务创建][az-acr-task-create]命令创建每日[计划的 ACR 任务](container-registry-tasks-scheduled.md)。 任务清除 7 天前在存储库中修改的`hello-world`标记。 容器命令使用环境变量传递。 任务在没有源上下文的情况下运行。

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

运行 [az acr task show][az-acr-task-show] 命令查看该计时器触发器是否已配置。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>清除大量标记和清单

清除大量标记和清单可能需要几分钟或更长时间。 要清除数千个标记和清单，该命令可能需要比按需任务的默认超时时间 600 秒长，或计划任务的 3600 秒。 如果超过超时时间，则仅删除标记和清单的子集。 为确保完成大规模清除，通过参数`--timeout`增加值。 

例如，以下按需任务设置 3600 秒（1 小时）的超时时间：

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>示例：注册表中多个存储库的计划清除

本示例遍走使用`acr purge`定期清理注册表中的多个存储库。 例如，您可能有一个开发管道，用于将映像推送到`samples/devimage1``samples/devimage2`和 存储库。 定期将开发映像导入到部署的生产存储库中，因此不再需要开发映像。 每周清除 和`samples/devimage1``samples/devimage2`存储库，为下周的工作做准备。

### <a name="preview-the-purge"></a>预览清除

在删除数据之前，我们建议使用 参数`--dry-run`运行按需清除任务。 此选项允许您查看该命令将清除的标记和清单，而无需删除任何数据。 

在下面的示例中，每个存储库中的筛选器选择所有标记。 该`--ago 0d`参数匹配存储库中与筛选器匹配的所有年龄的图像。 根据需要修改方案的选择条件。 参数`--untagged`指示删除除标记之外的清单。 容器命令使用环境变量传递给[az acr run][az-acr-run]命令。

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

查看命令输出以查看与选择参数匹配的标记和清单。 由于该命令使用`--dry-run`运行，因此不会删除任何数据。

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

验证干运行后，创建计划任务以自动清除。 以下示例将星期日 1：00 UTC 的每周任务安排以运行以前的清除命令：

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

运行 [az acr task show][az-acr-task-show] 命令查看该计时器触发器是否已配置。

## <a name="next-steps"></a>后续步骤

了解在 Azure 容器注册表中删除[图像数据](container-registry-delete.md)的其他选项。

有关映像存储的详细信息，请参阅[Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

