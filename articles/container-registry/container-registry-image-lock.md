---
title: 锁定图像
description: 为容器映像或存储库设置属性，使其在 Azure 容器注册表中无法删除或覆盖。
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 8eb2a549e9d9f3a7ed4a482ac6a9ea4ba61ea4f2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442220"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>锁定 Azure 容器注册表中的容器映像

在 Azure 容器注册表中，可以锁定映像版本或存储库，使其无法删除或更新。 若要锁定图像或存储库，请使用 Azure CLI 命令[az acr 存储库更新][az-acr-repository-update]更新其属性。 

本文要求在 Azure Cloud Shell 或本地运行 Azure CLI （建议使用版本2.0.55 或更高版本）。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 本文不适用于锁定整个注册表，例如，使用 Azure 门户中的**设置 > 锁定**，或在 Azure CLI 中 `az lock` 命令。 锁定注册表资源不会阻止你在存储库中创建、更新或删除数据。 锁定注册表只会影响管理操作，例如添加或删除复制或删除注册表本身。 [锁定资源以防止意外更改](../azure-resource-manager/resource-group-lock-resources.md)的详细信息。

## <a name="scenarios"></a>方案

默认情况下，Azure 容器注册表中的标记图像是*可变*的，因此，具有适当的权限，你可以使用相同的标记重复更新并推送到注册表。 还可以根据需要[删除](container-registry-delete.md)容器映像。 当你开发映像并且需要保持注册表大小时，此行为很有用。

但是，将容器映像部署到生产环境时，可能需要*不可变*的容器映像。 不可变的映像是不会意外删除或覆盖的映像。

有关在注册表中标记和版本映像的策略，请参阅标记[和版本控制容器映像的建议](container-registry-image-tag-version.md)。

使用[az acr repository update][az-acr-repository-update]命令设置存储库属性，以便能够：

* 锁定映像版本或整个存储库

* 保护映像版本或存储库不被删除，但允许更新

* 阻止对映像版本或整个存储库的读取（拉取）操作

有关示例，请参阅以下各节。 

## <a name="lock-an-image-or-repository"></a>锁定图像或存储库 

### <a name="show-the-current-repository-attributes"></a>显示当前存储库属性
若要查看存储库的当前属性，请运行以下[az acr repository show][az-acr-repository-show]命令：

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>显示当前图像属性
若要查看标记的当前属性，请运行以下[az acr repository show][az-acr-repository-show]命令：

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>按标记锁定图像

若要锁定*myregistry*中的*myrepo/myimage： tag*图像，请运行以下[az acr repository update][az-acr-repository-update]命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>按清单摘要锁定图像

若要锁定由清单摘要（SHA-256 哈希，表示为 `sha256:...`）标识的*myrepo/myimage*映像，请运行以下命令。 （若要查找与一个或多个图像标记相关联的清单摘要，请运行[az acr repository show-][az-acr-repository-show-manifests] manifest 命令。）

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>锁定存储库

若要锁定*myrepo/myimage*存储库以及其中的所有映像，请运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>防止删除图像或存储库

### <a name="protect-an-image-from-deletion"></a>保护映像不被删除

若要允许更新但不删除*myrepo/myimage： tag*图像，请运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>防止删除存储库

以下命令将设置*myrepo/myimage*存储库，因此不能将其删除。 仍可更新或删除单独的映像。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>阻止对映像或存储库进行读取操作

若要阻止对*myrepo/myimage： tag*映像执行读取（拉取）操作，请运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

若要阻止对*myrepo/myimage*存储库中的所有映像执行读取操作，请运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>解锁映像或存储库

若要还原*myrepo/myimage：标记*图像的默认行为，以便可以将其删除和更新，请运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

若要还原*myrepo/myimage*存储库和所有映像的默认行为，以便可以删除和更新这些映像，请运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用[az acr repository update][az-acr-repository-update]命令来防止删除或更新存储库中的映像版本。 若要设置其他属性，请参阅[az acr 存储库更新][az-acr-repository-update]命令参考。

若要查看为映像版本或存储库设置的属性，请使用[az acr repository show][az-acr-repository-show]命令。

有关删除操作的详细信息，请参阅[在 Azure 容器注册表中删除容器映像][container-registry-delete]。

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

