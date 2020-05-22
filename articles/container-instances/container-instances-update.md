---
title: 更新容器组
description: 了解如何更新 Azure 容器实例容器组中正在运行的容器。
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cfc27de8caae98dd1c3065b5ed06433c4baaa5d2
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928714"
---
# <a name="update-containers-in-azure-container-instances"></a>更新 Azure 容器实例中的容器

在正常操作容器实例期间，你可能会发现有必要更新[容器组](./container-instances-container-groups.md)中正在运行的容器。 例如，你可能希望更新一个属性（例如，映像版本、DNS 名称或环境变量），或者刷新其应用程序已崩溃的容器中的属性。

通过使用至少一个已修改的属性重新部署现有组，来更新正在运行的容器组中的容器。 更新某个容器组时，该组中所有正在运行的容器会就地重启（通常在同一基础容器主机上进行）。

> [!NOTE]
> 已终止或已删除的容器组无法更新。 容器组终止（处于“成功”或“失败”状态）或被删除后，必须将该组作为新组进行部署。 请参阅其他[限制](#limitations)。

## <a name="update-a-container-group"></a>更新容器组

若要更新现有容器组，请执行以下操作：

* 发出 create 命令（或使用 Azure 门户），指定现有组的名称 
* 修改或添加至少一个支持在重新部署时进行更新的组属性。 某些属性[不支持更新](#properties-that-require-container-delete)。
* 使用先前提供的值设置其他属性。 如果未为某个属性设置值，则该属性将恢复为其默认值。

> [!TIP]
> [YAML 文件](./container-instances-container-groups.md#deployment)有助于维护容器组的部署配置，你可以从其着手来部署已更新的组。 如果使用了其他方法来创建组，则可使用 [az container export][az-container-export] 将配置导出到 YAML。 

### <a name="example"></a>示例

以下 Azure CLI 示例更新具有新 DNS 名称标签的容器组。 由于该组的 DNS 名称标签属性是可以更新的属性，因此会重新部署容器组，并重启其容器。

具有 DNS 名称标签 *myapplication-staging* 的初始部署：

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

使用新的 DNS 名称标签“myapplication”更新容器组，并使用先前使用的值设置其余属性：

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>更新的好处

更新现有容器组的主要好处是加快部署速度。 重新部署现有容器组时，会从前一部署缓存的配置中提取该组的容器映像层。 此过程不会像新部署中一样从注册表中提取所有全新的映像层，而只会提取修改的层（如果有）。

如果更新容器组而不是删除再部署新的容器组，则基于大型容器映像的应用程序（例如 Windows Server Core）的部署速度会有明显的改善。

## <a name="limitations"></a>限制

* 并非容器组的所有属性都支持更新。 若要更改某个容器组的某些属性，必须先删除，再重新部署该组。 请参阅[需要容器删除操作的属性](#properties-that-require-container-delete)。
* 更新某个容器组时，该容器组中的所有容器会重启。 无法对多容器组中的特定容器执行更新或就地重启。
* 容器组的 IP 地址通常会在更新之后保留，但不能保证保持不变。 只要将容器组部署到相同的基础主机，容器组就会保留其 IP 地址。 有些 Azure 内部事件可能会导致容器组重新部署到其他主机，尽管这很少见。 为了避免此问题，建议对容器实例使用 DNS 名称标签。
* 已终止或已删除的容器组无法更新。 容器组停止（处于“已终止”状态）或被删除后，该组将作为新组进行部署。

## <a name="properties-that-require-container-delete"></a>需要容器删除操作的属性

并非所有容器组属性都可以更新。 例如，若要更改容器的重启策略，必须先删除容器组，然后再重新创建它。

更改以下属性需要在重新部署容器组前将容器组删除：

* OS 类型
* CPU、内存或 GPU 资源
* 重启策略
* 网络配置文件

删除再重新创建某个容器组时，不是“重新部署”该组，而是新建一个容器组。 将从注册表中全新提取所有映像层，而不是从前一个部署缓存的配置中提取。 由于部署到不同的基础主机，容器的 IP 地址也可能会更改。

## <a name="next-steps"></a>后续步骤

本文中多次提到了**容器组**。 Azure 容器实例中的每个容器部署在容器组中，容器组可以包含多个容器。

[Azure 容器实例中的容器组](./container-instances-container-groups.md)

[部署多容器组](container-instances-multi-container-group.md)

[手动停止或启动 Azure 容器实例中的容器](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
