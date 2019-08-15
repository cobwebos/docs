---
title: 删除 Azure 容器注册表中的映像资源
description: 详细说明如何通过使用 Azure CLI 命令删除容器映像数据来有效地管理注册表大小。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/31/2019
ms.author: danlep
ms.openlocfilehash: 12c1b5f9fa9620622b31f22c701d58ae237bcbf2
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035147"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 删除 Azure 容器注册表中的容器映像

要保持 Azure 容器注册表的大小不变，应定期删除过时的映像数据。 尽管部分部署到生产的容器映像可能需要存储更长时间，但通常可更快删除其他映像。 例如，在自动化生成和测试方案中，可使用从未部署的映像快速填充注册表，并可在完成生成和测试通过后不久即将其清除。

因为可通过多种不同的方式删除映像数据，请务必了解每个删除操作如何影响存储使用情况。 本文介绍几种删除图像数据的方法:

* 删除[存储库](#delete-repository)：删除存储库中的所有映像和所有唯一层。
* 按[标记](#delete-by-tag)删除：删除映像、其标记、其引用的所有唯一层，以及与其关联的所有其他标记。
* 按[清单摘要](#delete-by-manifest-digest)删除：删除映像、该映像引用的所有唯一层，以及与其关联的所有标记。

提供示例脚本是为了自动完成删除操作。

有关这些概念的介绍, 请参阅[关于注册表、存储库和映像](container-registry-concepts.md)。

## <a name="delete-repository"></a>删除存储库

删除存储库时，将一并删除存储库中的所有映像，包括所有标记、唯一层和清单。 删除存储库时, 会恢复在该存储库中引用唯一层的映像所使用的存储空间。

以下 Azure CLI 命令会删除“acr-helloworld”存储库，并删除该存储库中的所有标记和清单。 如果注册表中的任何其他图像未引用已删除的清单所引用的层, 则还会删除其层数据, 从而恢复存储空间。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>按标记删除

可通过在删除操作中指定存储库名称和标记，删除存储库中的单个映像。 如果按标记删除，将恢复该映像中任何唯一层所用的存储空间；唯一层是指不由存储库中的其他任何映像共享的层。

若要按标记删除, 请使用[az acr repository delete][az-acr-repository-delete] , 并在`--image`参数中指定映像名称。 此操作删除映像的所有唯一层以及与之关联的任何其他标记。

例如，从注册表“myregistry”中删除“acr-helloworld:latest”映像：

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> 按标记删除不应与删除标记（取消标记）混淆。 可以使用 Azure CLI 命令[az acr repository 取消标记][az-acr-repository-untag]删除标记。 取消标记映像时不释放任何空间，因为其[清单](container-registry-concepts.md#manifest)和层数据仍保留在注册表中。 仅删除标记引用本身。

## <a name="delete-by-manifest-digest"></a>按清单摘要删除

[清单摘要](container-registry-concepts.md#manifest-digest)可与零个、一个或多个标记相关联。 按摘要删除时，将删除清单引用的所有标记，正如映像的任何唯一层的层数据一样。 不删除共享的层数据。

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

接下来, 在[az acr repository delete][az-acr-repository-delete]命令中指定要删除的摘要。 该命令的格式如下：

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

例如，要删除上述输出（具有标记“v2”）中列出的最后一个清单：

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

从注册表中删除 `acr-helloworld:v2` 映像，正如该映像的任何唯一的层数据一样。 如果清单与多个标记关联，还删除所有相关联的标记。

## <a name="delete-digests-by-timestamp"></a>按时间戳删除摘要

若要保持存储库或注册表的大小，可能需要定期删除早于某个日期的清单摘要。

以下 Azure CLI 命令按升序列出存储库中早于指定时间戳的所有清单摘要。 将 `<acrName>` 和 `<repositoryName>` 替换为适合环境的值。 时间戳可能是完整的日期时间表达式，也可能是一个日期，如此示例所示。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

在确定过时的清单摘要以后，可以运行以下 Bash 脚本，删除早于指定时间戳的清单摘要。 它需要 Azure CLI 和 xargs。 默认情况下，该脚本不执行任何删除。 将 `ENABLE_DELETE` 值改为 `true` 以启用映像删除。

> [!WARNING]
> 请谨慎使用以下示例脚本，已删除映像数据是无法恢复的。 如果系统按清单摘要（而不是映像名称）拉取映像，则不应运行这些脚本。 删除清单摘要后，这些系统即无法从注册表拉取映像。 请考虑采用*唯一的标记*方案 ([建议的最佳做法](container-registry-image-tag-version.md)), 而不是按清单进行请求。 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>删除无标记的映像

如[清单摘要](container-registry-concepts.md#manifest-digest)部分中所述，使用现有标记推送已修改的映像时，会取消标记之前推送的映像，从而导致孤立（或“无关联”）的映像。 之前推送的映像的清单及其层数据仍保留在注册表中。 请注意以下事件序列：

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
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

正如上一步骤的输出中所示，现在存在一个 `"tags"` 属性为空列表的孤立清单。 此清单仍与其引用的任何唯一层数据一起位于注册表中。 要删除此类孤立映像及其层数据，必须按清单摘要删除。

## <a name="delete-all-untagged-images"></a>删除所有无标记的映像

可使用以下 Azure CLI 命令列出存储库中所有无标记的映像。 将 `<acrName>` 和 `<repositoryName>` 替换为适合环境的值。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

在脚本中使用此命令, 可以删除存储库中所有未标记的图像。

> [!WARNING]
> 请谨慎使用以下示例脚本，已删除映像数据是无法恢复的。 如果系统按清单摘要（而不是映像名称）拉取映像，则不应运行这些脚本。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 请考虑采用*唯一的标记*方案 ([建议的最佳做法](container-registry-image-tag-version.md)), 而不是按清单进行请求。

**Bash 中的 Azure CLI**

以下 Bash 脚本会删除存储库中所有无标记的映像。 它需要 Azure CLI 和 xargs。 默认情况下，该脚本不执行任何删除。 将 `ENABLE_DELETE` 值改为 `true` 以启用映像删除。

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
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**PowerShell 中的 Azure CLI**

以下 PowerShell 脚本会删除存储库中所有无标记的映像。 它需要 PowerShell 和 Azure CLI。 默认情况下，该脚本不执行任何删除。 将 `$enableDelete` 值改为 `$TRUE` 以启用映像删除。

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
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```

## <a name="automatically-purge-tags-and-manifests-preview"></a>自动清除标记和清单 (预览)

作为脚本 Azure CLI 命令的替代方法, 运行按需或计划的 ACR 任务来删除早于特定时间段或与指定名称筛选器匹配的所有标记。 有关详细信息, 请参阅[自动清除 Azure 容器注册表中的映像](container-registry-auto-purge.md)。

## <a name="next-steps"></a>后续步骤

要详细了解 Azure 容器注册表中的映像存储，请参阅 [Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
