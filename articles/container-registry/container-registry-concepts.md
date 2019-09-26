---
title: 关于 Azure 容器注册表中的存储库和映像
description: Azure 容器注册表、存储库和容器映像的重要概念简介。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/10/2019
ms.author: danlep
ms.openlocfilehash: 9a3d4a7d9c3fd4a0465d4e780024559a71372d9d
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300200"
---
# <a name="about-registries-repositories-and-images"></a>关于注册表、存储库和映像

本文介绍容器注册表、存储库和容器映像以及相关项目的重要概念。 

## <a name="registry"></a>注册表

容器注册表是一项存储和分发容器映像的服务。 Docker Hub 是一个公共容器注册表，支持开放源社区并充当映像的通用目录。 Azure 容器注册表为用户提供对其映像的直接控制，同时提供集成身份验证、支持全局分发的[异地复制](container-registry-geo-replication.md)，以及用于网络关闭部署、[虚拟网络和防火墙的可靠性配置](container-registry-vnet.md)、[标记锁定](container-registry-image-lock.md)以及其他许多增强功能。 

除了 Docker 容器映像以外，Azure 容器注册表还支持相关的[内容项目](container-registry-image-formats.md)，包括开放容器计划 (OCI) 映像格式。

## <a name="content-addressable-elements-of-an-artifact"></a>项目的内容可寻址元素

Azure 容器注册表中的项目地址包括以下元素。 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** - 注册表主机的完全限定名称。 Azure 容器注册表中的注册表主机的格式为*myregistry*. azurecr.io （全部小写）。 使用 Docker 或其他客户端工具将项目提取或推送到 Azure 容器注册表时，必须指定 loginUrl。 
* **namespace** - 以斜杠分隔的、相关映像或项目（例如工作组或应用）的逻辑分组
* **artifact** - 特定映像或项目的存储库名称
* **tag** - 存储在存储库中的映像或项目的特定版本


例如，Azure 容器注册表中映像的完整名称可能类似于：

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

有关这些元素的详细信息，请参阅以下部分。

## <a name="repository-name"></a>存储库名称

容器注册表管理存储库，并管理名称相同但标记不同的容器映像或其他项目的集合。 例如，以下三个映像位于“acr-helloworld”存储库中：

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

## <a name="image"></a>图像

注册表中的容器映像或其他项目与一个或多个标记相关联，具有一个或多个层，并由一个清单标识。 了解这些组件之间的关系有助于有效管理注册表。

### <a name="tag"></a>标记

映像或其他项目的标记指定了其版本。 存储库中的单个项目可分配有一个或多个标记，但也可能“无标记”。 也就是说，可删除映像中的所有标记，而映像的数据（其层）保留在注册表中。

映像的名称由存储库（或存储库和命名空间）和标记进行定义。 在推送或拉取操作中指定映像名称，可以推送和拉取映像。

如何对容器映像进行标记由其开发或部署方案引导。 例如，建议使用稳定标记来维护基础映像，使用唯一标记来部署映像。 有关详细信息，请参阅[有关对容器映像进行标记和版本控制的建议](container-registry-image-tag-version.md)。

### <a name="layer"></a>层

容器映像由一个或多个层构成，每个层对应于 Dockerfile 中定义该映像的某行。 注册表中的映像共享常用层，从而提高存储效率。 例如，不同存储库中的多个映像可能共享同一个 Alpine Linux 基础层，但注册表中仅存储该层的一个副本。

由于多个映像共享常用层，层共享也可优化到节点的层分发。 例如，如果某节点上已有的映像包含 Alpine Linux 层作为其基础层，则在后续拉取引用同一层的不同映像时，不会将层传输到节点。 相反，它会引用节点上已存在的层。

为了针对潜在的层操作提供安全隔离和保护，层不会在注册表之间共享。

### <a name="manifest"></a>清单

推送到容器注册表的每个容器映像或项目都与一个清单相关联。 推送映像时由注册表生成的清单唯一标识映像并指定其层。 可以使用 Azure CLI 命令 [az acr repository show-manifests][az-acr-repository-show-manifests] 列出存储库的清单：

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，列出 "acr-helloworld" 存储库的清单：

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

清单由唯一的 SHA-256 哈希（即清单摘要）进行标识。 每个映像或项目（无论是否标记）均由其摘要标识。 即便映像的层数据与其他映像的层数据相同，摘要值也是唯一的。 此机制使你能够反复向注册表推送标记相同的映像。 例如，你可反复向注册表推送 `myimage:latest` 而不出任何错误，因为每个映像均由其唯一摘要标识。

通过在拉取操作中指定映像的摘要，可从注册表拉取该映像。 某些系统可能配置为按摘要拉取，因为它保证即便后续向注册表推送标记相同的映像，仍将拉取映像版本。

例如，按清单摘要拉取“acr-helloworld”存储库中的映像：

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 如果反复推送修改后的标记相同的映像，可能创建孤立的映像；此类映像不带标记，但仍占用注册表中的空间。 按标记列出或查看映像时，Azure CLI 或 Azure 门户中不显示无标记的映像。 但是，它们的层仍然存在，且占用注册表中的空间。 删除未标记的图像会在清单是唯一的（或最后一个）指向特定层的情况下释放注册表空间。 有关释放未标记映像所用空间的信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 容器注册表中的[映像存储](container-registry-storage.md)和[支持的内容格式](container-registry-image-formats.md)。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


