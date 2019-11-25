---
title: Delete image resources
description: Details on how to effectively manage registry size by deleting container image data using Azure CLI commands.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8d20bf2be1d472855c3e67dd79ea1725c152e3d2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455267"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Delete container images in Azure Container Registry using the Azure CLI

要保持 Azure 容器注册表的大小不变，应定期删除过时的映像数据。 尽管部分部署到生产的容器映像可能需要存储更长时间，但通常可更快删除其他映像。 例如，在自动化生成和测试方案中，可使用从未部署的映像快速填充注册表，并可在完成生成和测试通过后不久即将其清除。

因为可通过多种不同的方式删除映像数据，请务必了解每个删除操作如何影响存储使用情况。 This article covers several methods for deleting image data:

* 删除[存储库](#delete-repository)：删除存储库中的所有映像和所有唯一层。
* 按[标记](#delete-by-tag)删除：删除映像、其标记、其引用的所有唯一层，以及与其关联的所有其他标记。
* 按[清单摘要](#delete-by-manifest-digest)删除：删除映像、该映像引用的所有唯一层，以及与其关联的所有标记。

Sample scripts are provided to help automate delete operations.

For an introduction to these concepts, see [About registries, repositories, and images](container-registry-concepts.md).

## <a name="delete-repository"></a>删除存储库

删除存储库时，将一并删除存储库中的所有映像，包括所有标记、唯一层和清单。 When you delete a repository, you recover the storage space used by the images that reference unique layers in that repository.

以下 Azure CLI 命令会删除“acr-helloworld”存储库，并删除该存储库中的所有标记和清单。 If layers referenced by the deleted manifests are not referenced by any other images in the registry, their layer data is also deleted, recovering the storage space.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>按标记删除

可通过在删除操作中指定存储库名称和标记，删除存储库中的单个映像。 如果按标记删除，将恢复该映像中任何唯一层所用的存储空间；唯一层是指不由存储库中的其他任何映像共享的层。

To delete by tag, use [az acr repository delete][az-acr-repository-delete] and specify the image name in the `--image` parameter. 此操作删除映像的所有唯一层以及与之关联的任何其他标记。

例如，从注册表“myregistry”中删除“acr-helloworld:latest”映像：

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> 按标记删除不应与删除标记（取消标记）混淆。 You can delete a tag with the Azure CLI command [az acr repository untag][az-acr-repository-untag]. No space is freed when you untag an image because its [manifest](container-registry-concepts.md#manifest) and layer data remain in the registry. 仅删除标记引用本身。

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

Next, specify the digest you wish to delete in the [az acr repository delete][az-acr-repository-delete] command. 该命令的格式如下：

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

例如，要删除上述输出（具有标记“v2”）中列出的最后一个清单：

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

The `acr-helloworld:v2` image is deleted from the registry, as is any layer data unique to that image. 如果清单与多个标记关联，还删除所有相关联的标记。

## <a name="delete-digests-by-timestamp"></a>Delete digests by timestamp

To maintain the size of a repository or registry, you might need to periodically delete manifest digests older than a certain date.

The following Azure CLI command lists all manifest digest in a repository older than a specified timestamp, in ascending order. 将 `<acrName>` 和 `<repositoryName>` 替换为适合环境的值。 The timestamp could be a full date-time expression or a date, as in this example.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

After identifying stale manifest digests, you can run the following Bash script to delete manifest digests older than a specified timestamp. 它需要 Azure CLI 和 xargs。 默认情况下，该脚本不执行任何删除。 将 `ENABLE_DELETE` 值改为 `true` 以启用映像删除。

> [!WARNING]
> Use the following sample script with caution--deleted image data is UNRECOVERABLE. If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. Deleting the manifest digests will prevent those systems from pulling the images from your registry. Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md). 

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

As you can see in the output of the last step in the sequence, there is now an orphaned manifest whose `"tags"` property is an empty list. 此清单仍与其引用的任何唯一层数据一起位于注册表中。 要删除此类孤立映像及其层数据，必须按清单摘要删除。

## <a name="delete-all-untagged-images"></a>删除所有无标记的映像

可使用以下 Azure CLI 命令列出存储库中所有无标记的映像。 将 `<acrName>` 和 `<repositoryName>` 替换为适合环境的值。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Using this command in a script, you can delete all untagged images in a repository.

> [!WARNING]
> 请谨慎使用以下示例脚本，已删除映像数据是无法恢复的。 If you have systems that pull images by manifest digest (as opposed to image name), you should not run these scripts. 删除无标的记映像后，这些系统即无法从注册表拉取映像。 Instead of pulling by manifest, consider adopting a *unique tagging* scheme, a [recommended best practice](container-registry-image-tag-version.md).

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


## <a name="automatically-purge-tags-and-manifests-preview"></a>自动清除标记和清单（预览版）

As an alternative to scripting Azure CLI commands, run an on-demand or scheduled ACR task to delete all tags that are older than a certain duration or match a specified name filter. For more information, see [Automatically purge images from an Azure container registry](container-registry-auto-purge.md).

Optionally set a [retention policy](container-registry-retention-policy.md) for each registry, to manage untagged manifests. When you enable a retention policy, image manifests in the registry that don't have any associated tags, and the underlying layer data, are automatically deleted after a set period.

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
