---
title: 更新 Azure 容器实例中的容器
description: 了解如何更新 Azure 容器实例容器组中正在运行的容器。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: 5a42b0983b0f754b119fa304317e758a976fb4f6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432610"
---
# <a name="update-containers-in-azure-container-instances"></a>更新 Azure 容器实例中的容器

在正常操作容器实例期间，你可能认为有必要更新容器组中的容器。 例如，你可能想要更新映像版本、更改 DNS 名称、更新环境变量，或刷新其应用程序已崩溃的容器的状态。

## <a name="update-a-container-group"></a>更新容器组

通过使用至少一个已修改的属性重新部署现有组，来更新容器组中的容器。 更新某个容器组时，该组中所有正在运行的容器会就地重启。

通过发出 create 命令（或使用 Azure 门户）并指定现有组的名称，来重新部署现有的容器组。 发出 create 命令时修改组的至少一个有效属性，以触发重新部署。 并非所有容器组属性都可用于重新部署。 有关不支持的属性列表，请参阅[需要删除操作的属性](#properties-that-require-delete)。

以下 Azure CLI 示例更新具有新 DNS 名称标签的容器组。 由于已修改该组的 DNS 名称标签属性，因此会重新部署容器组，并重启其容器。

具有 DNS 名称标签 *myapplication-staging* 的初始部署：

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

更新具有新 DNS 名称标签 *myapplication* 的容器组：

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>更新的好处

更新现有容器组的主要好处是加快部署速度。 重新部署现有容器组时，会从前一部署缓存的配置中提取该组的容器映像层。 此过程不会像新部署中一样从注册表中提取所有全新的映像层，而只会提取修改的层（如果有）。

如果更新容器组而不是删除再部署新的容器组，则基于大型容器映像的应用程序（例如 Windows Server Core）的部署速度会有明显的改善。

## <a name="limitations"></a>限制

并非容器组的所有属性都支持更新。 若要更改某个容器组的某些属性，必须先删除，再重新部署该组。 有关详细信息，请参阅[需要容器删除操作的属性](#properties-that-require-container-delete)。

更新某个容器组时，该容器组中的所有容器会重启。 无法对多容器组中的特定容器执行更新或就地重启。

每次更新后，容器的 IP 地址通常不会更改，但也不能保证该地址保持不变。 只要将容器组部署到相同的基础主机，容器组就会保留其 IP 地址。 尽管 Azure 容器实例会尽量重新部署到同一主机，但某些 Azure 内部事件可能导致重新部署到不同的主机（这种情况很少见）。 为了避免此问题，请始终对容器实例使用 DNS 名称标签。

已终止或已删除的容器组无法更新。 停止（处于“已终止”状态）或删除某个容器组后，将全新部署该组。

## <a name="properties-that-require-container-delete"></a>需要容器删除操作的属性

如前所述，并非所有容器组属性都可更新。 例如，若要更改端口或重启容器的策略，必须先删除容器组，然后重新创建。

以下属性在重新部署之前需要执行容器组删除操作：

* OS 类型
* CPU
* 内存
* 重启策略
* 端口

删除再重新创建某个容器组时，不是“重新部署”该组，而是新建一个容器组。 将从注册表中全新提取所有映像层，而不是从前一个部署缓存的配置中提取。 由于部署到不同的基础主机，容器的 IP 地址也可能会更改。

## <a name="next-steps"></a>后续步骤

本文中多次提到了**容器组**。 Azure 容器实例中的每个容器部署在容器组中，容器组可以包含多个容器。

[Azure 容器实例中的容器组](container-instances-container-groups.md)

[部署多容器组](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
