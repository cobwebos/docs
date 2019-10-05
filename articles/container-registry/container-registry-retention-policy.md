---
title: 用于在 Azure 容器注册表中保留未标记清单的策略
description: 了解如何启用 Azure 容器注册表中的保留策略，以在定义的时间段后自动删除未标记清单。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/02/2019
ms.author: danlep
ms.openlocfilehash: 79b3e48373114bfcee6dca2e6142f23bed1699e6
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972655"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>设置未标记清单的保留策略

Azure 容器注册表使你可以选择为不包含任何关联标记（未标记*清单*）的已存储映像清单设置*保留策略*。 启用保留策略后，会在你设置的天数后自动删除注册表中的未标记清单。 此功能可防止注册表填满不需要的项目，并有助于节省存储成本。 如果未标记的清单的 `delete-enabled` 特性设置为 `false`，则无法删除清单，且不应用保留策略。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装运行本文中的命令示例。 如果要在本地使用，则需要版本2.0.74 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 设置保留策略--删除的映像数据不可恢复。 如果你的系统按清单摘要（而不是映像名称）提取映像，则不应为未标记的清单设置保留策略。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳做法](container-registry-image-tag-version.md)，即“唯一标记”方案。

## <a name="preview-limitations"></a>预览版限制

* 只有**高级**容器注册表才能使用保留策略进行配置。 有关注册表服务层的信息，请参阅[Azure 容器注册表 sku](container-registry-skus.md)。
* 只能为未标记的清单设置保留策略。
* 保留策略目前仅适用于启用策略*后*未标记的清单。 注册表中的现有未标记清单不受策略限制。 若要删除现有未标记的清单，请参阅在[Azure 容器注册表中删除容器映像](container-registry-delete.md)中的示例。

## <a name="about-the-retention-policy"></a>关于保留策略

Azure 容器注册表对注册表中的清单进行引用计数。 当清单未加标签时，它会检查保留策略。 如果启用保留策略，则会根据策略中设置的天数，将清单删除操作排入队列，使用特定的日期。

单独的队列管理作业会持续处理消息，并根据需要进行缩放。 例如，假设你在注册表中将两个清单分别标记为1小时，保留策略为30天。 两条消息将被排入队列。 然后，大约30天后，将从队列中检索消息并进行处理，假设策略仍有效。

## <a name="set-a-retention-policy---cli"></a>设置保留策略-CLI

下面的示例演示如何使用 Azure CLI 为注册表中未标记的清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

默认情况下，容器注册表中未设置保留策略。 若要设置或更新保留策略，请在 Azure CLI 中运行[az acr config 留成 update][az-acr-config-retention-update]命令。 可以指定介于0到365之间的天数，以保留未标记清单。 如果未指定天数，则该命令会将默认值设置为7天。 保持期结束后，注册表中所有未标记的清单将自动删除。

以下示例在注册表*myregistry*中为未标记的清单设置30天的保留策略：

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

下面的示例将策略设置为在无标记后立即删除注册表中的所有清单。 通过将保留期设置为0天来创建此策略。 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>验证保留策略

如果在保留期为0天的情况下启用上述策略，则可以快速验证是否已删除未标记的清单：

1. 将测试映像 @no__t 0 映像推送到注册表，或替换所选的另一个测试映像。
1. @No__t 取消标记，例如，使用[az acr repository 取消标记][az-acr-repository-untag]命令。 未标记的清单将保留在注册表中。
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 几秒钟内，未标记的清单将被删除。 可以通过列出存储库中的清单（例如，使用[az acr 存储库 show-清单][az-acr-repository-show-manifests]命令）来验证删除。 如果测试映像只是存储库中的映像，则会删除存储库本身。

### <a name="disable-a-retention-policy"></a>禁用保留策略

若要查看注册表中设置的保留策略，请运行[az acr config 保持显示][az-acr-config-retention-show]命令：

```azurecli
az acr config retention show --registry myregistry
```

若要在注册表中禁用保留策略，请运行[az acr config 留成 update][az-acr-config-retention-update]命令并设置 `--status disabled`：

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>设置保留策略-门户

你还可以在[Azure 门户](https://portal.azure.com)中设置注册表的保留策略。 下面的示例演示如何使用门户为注册表中的未标记清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

1. 导航到 Azure 容器注册表。 在 "**策略**" 下，选择 "**保留**（预览版）"。
1. 在 "**状态**" 中，选择 "**已启用**"。
1. 选择0到365之间的天数，以保留未标记清单。 选择“保存”。

![启用 Azure 门户的保留策略](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>禁用保留策略

1. 导航到 Azure 容器注册表。 在 "**策略**" 下，选择 "**保留**（预览版）"。
1. 在 "**状态**" 中，选择 "**禁用**"。 选择“保存”。

## <a name="next-steps"></a>后续步骤

* 了解有关删除 Azure 容器注册表中的[映像和存储库](container-registry-delete.md)的选项的详细信息

* 了解如何从注册表[自动清除](container-registry-auto-purge.md)所选的映像和清单

* 了解有关在注册表中[锁定图像和清单](container-registry-image-lock.md)的选项的详细信息

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
