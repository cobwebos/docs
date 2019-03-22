---
title: 锁定 Azure 容器注册表中的图像
description: 设置属性的容器映像或存储库，因此它不能被删除或覆盖 Azure 容器注册表中。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: cdf457eefc88edcc22f1fbaab4859fbcf3b69bca
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56654620"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>锁定在 Azure 容器注册表中的容器映像

在 Azure 容器注册表，可以锁定映像版本或存储库，以便它不能被删除或更新。 若要锁定图像或存储库，请更新其属性使用 Azure CLI 命令[az acr 存储库更新][az-acr-repository-update]。 

本文要求运行 Azure CLI 在 Azure Cloud Shell 中或本地 (版本 2.0.55 或建议使用更高版本)。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

## <a name="scenarios"></a>方案

默认情况下，Azure 容器注册表中标记的映像是*可变*，因此可以反复更新并将具有相同的标记的映像推送到注册表具有相应权限。 也可以是容器映像[删除](container-registry-delete.md)根据需要。 当你开发映像，并需要保持注册表的大小，此行为很有用。

但是，在容器映像部署到生产环境时，你可能需要*不可变*容器映像。 不可变映像是指不能意外删除或覆盖。 使用[az acr 存储库更新][ az-acr-repository-update]命令以设置存储库属性，以便您可以：

* 锁定的映像版本或整个存储库

* 防止你的映像版本或存储库被删除，但允许更新

* 防止读取 （拉取） 的映像版本或整个存储库上的操作

请参阅以下各节的示例。

## <a name="lock-an-image-or-repository"></a>锁定图像或存储库 

### <a name="lock-an-image-by-tag"></a>通过标记来锁定图像

与锁*myrepo / myimage:tag*在图像*myregistry*，运行以下[az acr 存储库更新][ az-acr-repository-update]命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>通过清单摘要锁定图像

与锁*myrepo/myimage*映像由清单摘要 (SHA-256 哈希，表示为`sha256:...`)，运行以下命令。 (若要查找与一个或多个图像标记相关联的清单摘要，请运行[az acr 存储库显示清单][ az-acr-repository-show-manifests]命令。)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>锁定存储库

与锁*myrepo/myimage*存储库和所有映像中，运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>防止删除图像或存储库

### <a name="protect-an-image-from-deletion"></a>防止删除映像

若要允许*myrepo / myimage:tag*映像以更新但未被删除，运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>防止删除存储库

下面的命令集*myrepo/myimage*存储库，以便它不能删除。 仍可以更新或删除各个图像。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>防止读取的操作上的图像或存储库

若要防止在读取 （拉取） 操作*myrepo / myimage:tag*图中，运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

若要防止读取的操作中的所有映像*myrepo/myimage*存储库中，运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>解锁图像或存储库

若要还原默认行为*myrepo / myimage:tag*图像，以便将其删除并更新，运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

若要还原默认行为*myrepo/myimage*存储库和所有映像，以便他们可以删除和更新，运行以下命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何使用[az acr 存储库更新][ az-acr-repository-update]命令，以防止删除或更新存储库中的映像版本。 若要设置其他属性，请参阅[az acr 存储库更新][ az-acr-repository-update]命令参考。

若要查看的属性集的映像版本或存储库，请使用[az acr 存储库 show] [ az-acr-repository-show]命令。

有关删除操作的详细信息，请参阅[删除 Azure 容器注册表中的容器映像][container-registry-delete]。

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

