---
title: About repositories & images
description: Introduction to key concepts of Azure container registries, repositories, and container images.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455288"
---
# <a name="about-registries-repositories-and-images"></a>About registries, repositories, and images

This article introduces the key concepts of container registries, repositories, and container images and related artifacts. 

## <a name="registry"></a>注册表

容器注册表是一项存储和分发容器映像的服务。 Docker Hub is a public container registry that supports the open source community and serves as a general catalog of images. Azure Container Registry provides users with direct control of their images, with integrated authentication, [geo-replication](container-registry-geo-replication.md) supporting global distribution and reliability for network-close deployments, [virtual network and firewall configuration](container-registry-vnet.md), [tag locking](container-registry-image-lock.md), and many other enhanced features. 

In addition to Docker container images, Azure Container Registry supports related [content artifacts](container-registry-image-formats.md) including Open Container Initiative (OCI) image formats.

## <a name="content-addressable-elements-of-an-artifact"></a>Content addressable elements of an artifact

The address of an artifact in an Azure container registry includes the following elements. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - The fully qualified name of the registry host. The registry host in an Azure container registry is in the format *myregistry*.azurecr.io (all lowercase). You must specify the loginUrl when using Docker or other client tools to pull or push artifacts to an Azure container registry. 
* **namespace** - Slash-delimited logical grouping of related images or artifacts - for example, for a workgroup or app
* **artifact** - The name of a repository for a particular image or artifact
* **tag** - A specific version of an image or artifact stored in a repository


For example, the full name of an image in an Azure container registry might look like:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

See the following sections for details about these elements.

## <a name="repository-name"></a>存储库名称

Container registries manage *repositories*, collections of container images or other artifacts with the same name, but different tags. 例如，以下三个映像位于“acr-helloworld”存储库中：

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

存储库名称还可包括[命名空间](container-registry-best-practices.md#repository-namespaces)。 Namespaces allow you to group images using forward slash-delimited repository names, for example:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>图像

A container image or other artifact within a registry is associated with one or more tags, has one or more layers, and is identified by a manifest. Understanding how these components relate to each other can help you manage your registry effectively.

### <a name="tag"></a>标记

The *tag* for an image or other artifact specifies its version. A single artifact within a repository can be assigned one or many tags, and may also be "untagged." That is, you can delete all tags from an image, while the image's data (its layers) remain in the registry.

映像的名称由存储库（或存储库和命名空间）和标记进行定义。 在推送或拉取操作中指定映像名称，可以推送和拉取映像。

How you tag container images is guided by your scenarios to develop or deploy them. For example, stable tags are recommended for maintaining your base images, and unique tags for deploying images. For more information, see [Recommendations for tagging and versioning container images](container-registry-image-tag-version.md).

### <a name="layer"></a>层

Container images are made up of one or more *layers*, each corresponding to a line in the Dockerfile that defines the image. 注册表中的映像共享常用层，从而提高存储效率。 例如，不同存储库中的多个映像可能共享同一个 Alpine Linux 基础层，但注册表中仅存储该层的一个副本。

由于多个映像共享常用层，层共享也可优化到节点的层分发。 例如，如果某节点上已有的映像包含 Alpine Linux 层作为其基础层，则在后续拉取引用同一层的不同映像时，不会将层传输到节点。 相反，它会引用节点上已存在的层。

To provide secure isolation and protection from potential layer manipulation, layers are not shared across registries.

### <a name="manifest"></a>清单

Each container image or artifact pushed to a container registry is associated with a *manifest*. 推送映像时由注册表生成的清单唯一标识映像并指定其层。 You can list the manifests for a repository with the Azure CLI command [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

For example, list the manifests for the "acr-helloworld" repository:

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

### <a name="manifest-digest"></a>清单摘要

清单由唯一的 SHA-256 哈希（即清单摘要）进行标识。 Each image or artifact--whether tagged or not--is identified by its digest. 即便映像的层数据与其他映像的层数据相同，摘要值也是唯一的。 此机制使你能够反复向注册表推送标记相同的映像。 例如，你可反复向注册表推送 `myimage:latest` 而不出任何错误，因为每个映像均由其唯一摘要标识。

通过在拉取操作中指定映像的摘要，可从注册表拉取该映像。 某些系统可能配置为按摘要拉取，因为它保证即便后续向注册表推送标记相同的映像，仍将拉取映像版本。

For example, pull an image from the "acr-helloworld" repository by manifest digest:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 如果反复推送修改后的标记相同的映像，可能创建孤立的映像；此类映像不带标记，但仍占用注册表中的空间。 按标记列出或查看映像时，Azure CLI 或 Azure 门户中不显示无标记的映像。 但是，它们的层仍然存在，且占用注册表中的空间。 Deleting an untagged image frees registry space when the manifest is the only one, or the last one, pointing to a particular layer. For information about freeing space used by untagged images, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>后续步骤

Learn more about [image storage](container-registry-storage.md) and [supported content formats](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


