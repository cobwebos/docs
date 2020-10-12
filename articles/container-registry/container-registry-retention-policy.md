---
title: 保留未标记清单的策略
description: 了解如何在 Azure 容器注册表中启用保留策略，以便在定义期间后自动删除未标记的清单。
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 5dda85934bb10cf16fd90381539b892df4f5445c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83683461"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>设置未标记清单的保留策略

Azure 容器注册表为你提供了为没有任何关联标记（未标记清单）的存储映像清单设置保留策略的选项 。 启用保留策略后，注册表中的未标记清单会在设置的数天后自动删除。 此功能可防止注册表填满不需要的项目，并有助于节省存储成本。 如果将未标记清单的 `delete-enabled` 属性设置为 `false`，则无法删除清单，并且保留策略不适用。

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装来运行本文中的命令示例。 如果想要在本地使用它，则需要使用 2.0.74 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

保留策略是高级容器注册表的一项功能。 有关注册表服务层级的信息，请参阅 [Azure 容器注册表服务层](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 谨慎设置保留策略 - 删除的映像数据是无法恢复的。 如果系统按清单摘要（而不是映像名称）拉取映像，则不应为未标记的清单设置保留策略。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳做法](container-registry-image-tag-version.md)，即唯一标记方案。

## <a name="preview-limitations"></a>预览版限制

* 仅可以设置未标记清单的保留策略。
* 保留策略当前仅适用于启用策略后未标记的清单。 注册表中现有的未标记清单不受策略约束。 要删除现有的未标记清单，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)中的示例。

## <a name="about-the-retention-policy"></a>关于保留策略

Azure 容器注册表对注册表中的清单进行引用计数。 当未标记清单时，它将检查保留策略。 如果启用了保留策略，则会根据策略中设置的天数，将清单删除操作排入队列，并带有特定日期。

单独的队列管理作业会不断处理消息，根据需要进行缩放。 例如，假设你在保留策略为 30 天的注册表中未标记两个清单（相隔 1 小时）。 将两条消息排入队列。 然后，30 天后，大约相隔 1 小时，将从队列中检索并处理消息，前提是策略仍然有效。

## <a name="set-a-retention-policy---cli"></a>设置保留策略 - CLI

下面的示例演示如何使用 Azure CLI 为注册表中未标记的清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

默认情况下，容器注册表中不设置保留策略。 要设置或更新保留政策，请在 Azure CLI 中运行 [az acr config retention update][az-acr-config-retention-update] 命令。 可以指定介于 0 和 365 之间的天数来保留未标记的清单。 如果未指定天数，则该命令会将默认值设置为 7 天。 保留期后，注册表中的所有未标记清单将自动删除。

以下示例为注册表 myregistry 中未标记的清单设置 30 天的保留策略：

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

下面的示例设置一个策略，以便在未标记时立即删除注册表中的任何清单。 通过设置 0 天的保留期来创建此策略。 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>验证保留策略

如果启用保留期为 0 天的上述策略，则可以快速验证未标记的清单是否被删除：

1. 将测试映像 `hello-world:latest` 映像推送到注册表，或替换所选的其他测试映像。
1. 取消标记 `hello-world:latest` 映像，例如，使用 [az acr repository untag][az-acr-repository-untag] 命令。 未标记的清单将保留在注册表中。
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 在几秒钟内，未标记的清单将被删除。 可以通过在存储库中列出清单来验证删除，例如，使用 [z acr repository show-manifests][az-acr-repository-show-manifests] 命令。 如果测试映像是存储库中唯一的映像，则存储库本身将被删除。

### <a name="disable-a-retention-policy"></a>禁用保留策略

要查看注册表中设置的保留策略，请运行 [az acr config retention show][az-acr-config-retention-show] 命令：

```azurecli
az acr config retention show --registry myregistry
```

要禁用注册表中的保留策略，请运行 [az acr config retention update][az-acr-config-retention-update] 命令并设置 `--status disabled`：

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>设置保留策略 - 门户

还可以在 [Azure 门户](https://portal.azure.com)中设置注册表的保留策略。 下面的示例演示如何使用门户为注册表中未标记的清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

1. 导航到 Azure 容器注册表。 在“策略”下，选择“保留”（预览） 。
1. 在“状态”中，选择“启用” 。
1. 选择介于 0 和 365 之间的天数来保留未标记的清单。 选择“保存”。

![在 Azure 门户中启用保留策略](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>禁用保留策略

1. 导航到 Azure 容器注册表。 在“策略”下，选择“保留”（预览） 。
1. 在“状态”中，选择“禁用” 。 选择“保存”。

## <a name="next-steps"></a>后续步骤

* 详细了解 Azure 容器注册表中的[删除映像和存储库](container-registry-delete.md)的选项

* 了解如何[自动清除](container-registry-auto-purge.md)注册表中所选的映像和清单

* 详细了解注册表中[锁定映像和清单](container-registry-image-lock.md)的选项

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
