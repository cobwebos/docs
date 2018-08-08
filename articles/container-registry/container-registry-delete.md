---
title: 删除 Azure 容器注册表中的映像资源
description: 详细介绍如何通过删除容器映像数据有效管理注册表大小。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 07/27/2018
ms.author: marsma
ms.openlocfilehash: 6ab667a01eddd84d1145868a3ae499e7497035c9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266632"
---
# <a name="delete-container-images-in-azure-container-registry"></a>删除 Azure 容器注册表中的容器映像

要保持 Azure 容器注册表的大小不变，应定期删除过时的映像数据。 尽管部分部署到生产的容器映像可能需要存储更长时间，但通常可更快删除其他映像。 例如，在自动化生成和测试方案中，可使用从未部署的映像快速填充注册表，并可在完成生成和测试通过后不久即将其清除。

因为可通过多种不同的方式删除映像数据，请务必了解每个删除操作如何影响存储使用情况。 本文首先介绍 Docker 注册表和容器映像的组件，然后介绍删除映像数据的多种方法。

## <a name="registry"></a>注册表

容器注册表是一项存储和分发容器映像的服务。 Docker 中心是一个公共的 Docker 容器注册表，而 Azure 容器注册表在 Azure 中提供专用的 Docker 容器注册表。

## <a name="repository"></a>存储库

容器注册表可管理存储库，还可管理名称相同但标记不同的容器映像的集合。 例如，以下三个映像位于“acr-helloworld”存储库中：

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

存储库名称还可包括[命名空间](container-registry-best-practices.md#repository-namespaces)。 借助命名空间，可使用正斜杠分隔的存储库名称分组映像，例如：

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="components-of-an-image"></a>映像组件

注册表中的容器映像与一个或多个标记相关联，具有一个或多个层，并由一个清单标识。 了解这些组件如何彼此关联可助你确定用于释放注册表中的空间的最佳方法。

### <a name="tag"></a>标记

映像的标记指定其版本。 存储库中的单个映像可分配有一个或多个标记，但也可能“无标记”。 也就是说，可删除映像中的所有标记，而映像的数据（其层）保留注册表。

映像的名称由存储库（或存储库和命名空间）和标记进行定义。 在推送或拉取操作中指定映像名称，可以推送和拉取映像。

在专用注册表（如 Azure 容器注册表）中，映像名称还包括注册表主机的完全限定的名称。 ACR 中映像的注册表主机采用 acrname.azurecr.io 格式。 例如，上一节中“市场营销”命名空间中第一个映像的完整名称为：

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

有关映像标记最佳实践的讨论，请参阅 MSDN 上的 [Docker 标记：标记和版本控制 Docker 映像的最佳实践][tagging-best-practices]博客文章。

### <a name="layer"></a>层

映像由一个或多个层构成，每层都对应 Dockerfile 中定义该映像的某行。 注册表中的映像共享常用层，从而提高存储效率。 例如，不同存储库中的多个映像可能共享同一个 Alpine Linux 基础层，但注册表中仅存储该层的一个副本。

由于多个映像共享常用层，层共享也可优化到节点的层分发。 例如，如果某节点上已有的映像包含 Alpine Linux 层作为其基础层，则在后续拉取引用同一层的不同映像时，不会将层传输到节点。 相反，它会引用节点上已存在的层。

### <a name="manifest"></a>清单

推送到容器注册表的每个容器都与一个清单相关联。 推送映像时由注册表生成的清单唯一标识映像并指定其层。 可使用 Azure CLI 命令 [az acr repository show-manifests][az-acr-repository-show-manifests] 列出存储库的清单：

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，列出“acr-helloworld”存储库的清单摘要：

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

此处谈论的清单与可在 Azure 门户或使用 [Docker 清单检查][docker-manifest-inspect]查看的映像清单不同。 在下一节中，“清单摘要”指的是推送操作生成的摘要，而不是映像清单中的 config.digest。 可按清单摘要（而不是 config.digest）拉取和删除映像。 下图说明了两种类型的摘要。

![Azure 门户中的清单摘要和 config.digest][manifest-digest]

### <a name="manifest-digest"></a>清单摘要

清单由唯一的 SHA-256 哈希（即清单摘要）进行标识。 每个映像（无论是否标记）均由其摘要标识。 即便映像的层数据与其他映像的层数据相同，摘要值也是唯一的。 此机制使你能够反复向注册表推送标记相同的映像。 例如，你可反复向注册表推送 `myimage:latest` 而不出任何错误，因为每个映像均由其唯一摘要标识。

通过在拉取操作中指定映像的摘要，可从注册表拉取该映像。 某些系统可能配置为按摘要拉取，因为它保证即便后续向注册表推送标记相同的映像，仍将拉取映像版本。

例如，按清单摘要拉取“acr-helloworld”存储库中的映像：

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 如果反复推送修改后的标记相同的映像，可能创建孤立的映像；此类映像不带标记，但仍占用注册表中的空间。 按标记列出或查看映像时，Azure CLI 或 Azure 门户中不显示无标记的映像。 但是，它们的层仍然存在，且占用注册表中的空间。 要了解如何释放无标记的映像所占用的空间，请参阅本文中的[删除无标记的映像](#delete-untagged-images)部分。

## <a name="delete-image-data"></a>删除映像数据

可通过多种方式删除容器注册表中的映像数据：

* 删除[存储库](#delete-repository)：删除存储库中的所有映像和所有唯一层。
* 按[标记](#delete-by-tag)删除：删除映像、其标记、其引用的所有唯一层，以及与其关联的所有其他标记。
* 按[清单摘要](#delete-by-manifest-digest)删除：删除映像、该映像引用的所有唯一层，以及与其关联的所有标记。

## <a name="delete-repository"></a>删除存储库

删除存储库时，将一并删除存储库中的所有映像，包括所有标记、唯一层和清单。 删除存储库时，将还原该存储库中的映像所用的存储空间。

以下 Azure CLI 命令会删除“acr-helloworld”存储库，并删除该存储库中的所有标记和清单。 如果已删除清单所引用的层未由存储库中的其他任何映像引用，则还删除其层数据。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>按标记删除

可通过在删除操作中指定存储库名称和标记，删除存储库中的单个映像。 如果按标记删除，将恢复该映像中任何唯一层所用的存储空间；唯一层是指不由存储库中的其他任何映像共享的层。

要按标记删除，请使用 [az acr repository delete][az-acr-repository-delete]，并在 `--image` 参数中指定映像名称。 此操作删除映像的所有唯一层以及与之关联的任何其他标记。

例如，从注册表“myregistry”中删除“acr-helloworld:latest”映像：

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> 按标记删除不应与删除标记（取消标记）混淆。 可使用 Azure CLI 命令 [az acr repository untag][az-acr-repository-untag] 删除标记。 取消标记映像时不释放任何空间，因为其[清单](#manifest)和层数据仍保留在注册表中。 仅删除标记引用本身。

## <a name="delete-by-manifest-digest"></a>按清单摘要删除

[清单摘要](#manifest-digest)可与零个、一个或多个标记相关联。 按摘要删除时，将删除清单引用的所有标记，正如映像的任何唯一层的层数据一样。 不删除共享的层数据。

要按摘要删除，请先列出包含想要删除的映像的存储库清单摘要。 例如：

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

接下来，在 [az acr repository delete][az-acr-repository-delete] 命令中指定要删除的摘要。 该命令的格式如下：

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

例如，要删除上述输出（具有标记“v2”）中列出的最后一个清单：

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

从注册表中删除“acr-helloworld:v2”映像，正如该映像的任何唯一的层数据一样。 如果清单与多个标记关联，还删除所有相关联的标记。

## <a name="delete-untagged-images"></a>删除无标记的映像

如[清单摘要](#manifest-digest)部分中所述，使用现有标记推送已修改的映像时，会取消标记之前推送的映像，从而导致孤立（或“无关联”）的映像。 之前推送的映像的清单及其层数据仍保留在注册表中。 请注意以下事件序列：

1. 推送具有标记 latest 的映像 acr-helloworld：`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 检查存储库 acr-helloworld 的清单：

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. 修改 acr-helloworld Dockerfile
1. 推送具有标记 latest 的映像 acr-helloworld：`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 检查存储库 acr-helloworld 的清单：

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": null,
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

正如序列中上一步骤的输出中所示，现在存在一个 `"tags"` 属性为 `null` 的孤立清单。 此清单仍与其引用的任何唯一层数据一起位于注册表中。 要删除此类孤立映像及其层数据，必须按清单摘要删除。

### <a name="list-untagged-images"></a>列出无标记的映像

可使用以下 Azure CLI 命令列出存储库中所有无标记的映像。 将 `<acrName>` 和 `<repositoryName>` 替换为适合环境的值。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>  --query "[?tags==null].digest"
```

### <a name="delete-all-untagged-images"></a>删除所有无标记的映像

请谨慎使用以下示例脚本，已删除映像数据是无法恢复的。

**Bash 中的 Azure CLI**

以下 Bash 脚本会删除存储库中所有无标记的映像。 它需要 Azure CLI 和 xargs。 默认情况下，该脚本不执行任何删除。 将 `ENABLE_DELETE` 值改为 `true` 以启用映像删除。

> [!WARNING]
> 如果系统按清单摘要（而不是映像名称）拉取映像，则不应运行此脚本。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳实践][，即唯一标记方案tagging-best-practices]。

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**PowerShell 中的 Azure CLI**

以下 PowerShell 脚本会删除存储库中所有无标记的映像。 它需要 PowerShell 和 Azure CLI。 默认情况下，该脚本不执行任何删除。 将 `$enableDelete` 值改为 `$TRUE` 以启用映像删除。

> [!WARNING]
> 如果系统按清单摘要（而不是映像名称）拉取映像，则不应运行此脚本。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳实践][，即唯一标记方案tagging-best-practices]。

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>后续步骤

要详细了解 Azure 容器注册表中的映像存储，请参阅 [Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
