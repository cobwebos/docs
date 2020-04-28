---
title: 用于保留未标记清单的策略
description: 了解如何在 Azure 容器注册表中启用保留策略，以便在定义的期限过后自动删除未标记的清单。
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74454816"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>针对未标记的清单设置保留策略

Azure 容器注册表提供相应的选项让你针对存储的没有任何关联标记的映像清单（未标记的清单）设置保留策略。   启用保留策略后，注册表中未标记的清单将在设置的天数之后自动删除。 此功能可防止在注册表填满不需要的项目，并有助于节省存储成本。 如果未标记清单的 `delete-enabled` 属性设置为 `false`，则无法删除该清单，且不会应用保留策略。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装运行本文中的命令示例。 若要在本地使用 Azure CLI，需要安装 2.0.74 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 请谨慎设置保留策略 -- 已删除的映像数据不可恢复。 如果系统按清单摘要（而不是映像名称）提取映像，请不要针对未标记的清单设置保留策略。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳做法](container-registry-image-tag-version.md)，即“唯一标记”方案  。

## <a name="preview-limitations"></a>预览版限制

* 只能在“高级”版容器注册表中配置保留策略。  有关注册表服务层级的信息，请参阅 [Azure 容器注册表 SKU](container-registry-skus.md)。
* 只能针对未标记的清单设置保留策略。
* 目前，保留策略只会应用到在启用该策略之后未标记的清单。  注册表中的现有未标记清单不受该策略的影响。 若要删除现有的未标记清单，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)中的示例。

## <a name="about-the-retention-policy"></a>关于保留策略

Azure 容器注册表针对注册表中的清单执行引用计数。 当某个清单未标记时，容器注册表会检查保留策略。 如果启用了保留策略，则会根据该策略中设置的天数，将清单删除操作排队到特定的日期。

单独的队列管理作业会持续处理消息，并根据需要进行缩放。 例如，假设注册表中在过去 1 小时有两个清单未标记，保留策略为 30 天。 则有两条消息被排入队列。 30 天后，假设该策略仍然生效，将从队列中检索并处理这些消息（大约会提前 1 小时）。

## <a name="set-a-retention-policy---cli"></a>设置保留策略 - CLI

以下示例演示如何使用 Azure CLI 针对注册表中未标记的清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

默认不会在容器注册表中设置任何保留策略。 若要设置或更新保留策略，请在 Azure CLI 中运行 [az acr config retention update][az-acr-config-retention-update] 命令。 可以指定 0 到 365 天作为未标记清单的保留期。 如果未指定天数，该命令将设置默认 7 天。 保留期过后，注册表中所有未标记的清单将自动删除。

以下示例针对注册表 *myregistry* 中的未标记清单设置 30 天保留策略：

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

以下示例将策略设置为立即删除注册表中任何未标记的清单。 将保留期设置为 0 天即可创建此策略。 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>验证保留策略

如果启用上述保留期为 0 天的策略，可以快速验证是否已删除未标记的清单：

1. 将一个测试映像 `hello-world:latest` 推送到注册表，或替换为所选的另一个测试映像。
1. 取消标记 `hello-world:latest` 映像（例如，使用 [az acr repository untag][az-acr-repository-untag] 命令）。 未标记的清单将保留在注册表中。
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 几秒钟内，未标记的清单将被删除。 可以通过列出存储库中的清单来验证删除结果（例如，使用 [az acr repository show-manifests][az-acr-repository-show-manifests] 命令）。 如果该测试映像是存储库中仅有的一个映像，则会删除存储库本身。

### <a name="disable-a-retention-policy"></a>禁用保留策略

若要查看注册表中设置的保留策略，请运行 [az acr config retention show][az-acr-config-retention-show] 命令：

```azurecli
az acr config retention show --registry myregistry
```

若要禁用注册表中的保留策略，请运行 [az acr config retention update][az-acr-config-retention-update] 命令并设置 `--status disabled`：

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

* 详细了解用于在 Azure 容器注册表中[删除映像和存储库](container-registry-delete.md)的选项

* 了解如何从注册表[自动清除](container-registry-auto-purge.md)所选的映像和清单

* 详细了解用于在注册表中[锁定映像和清单](container-registry-image-lock.md)的选项

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
