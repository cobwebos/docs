---
title: 有关存储库和 Azure 容器注册表中的映像
description: Azure 容器注册表，存储库，容器映像的关键概念的简介。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800323"
---
# <a name="about-registries-repositories-and-images"></a>有关注册表、 存储库和映像

本文介绍了容器注册表，存储库，容器映像和相关的项目的关键概念。 

## <a name="registry"></a>注册表

容器注册表是一项存储和分发容器映像的服务  。 Docker 中心是，支持开源社区并作为常规的映像目录的公共容器注册表。 Azure 容器注册表提供具有直接控制其映像，通过集成身份验证的用户[异地复制](container-registry-geo-replication.md)支持全球分布和可靠性的近网络部署[虚拟网络和防火墙配置](container-registry-vnet.md)，[标记锁定](container-registry-image-lock.md)，以及许多其他增强功能。 

Azure 容器注册表支持相关的 Docker 容器映像，除了[内容项目](container-registry-image-formats.md)包括开放容器计划 (OCI) 图像格式。

## <a name="content-addressable-elements-of-an-artifact"></a>项目的内容可寻址元素

Azure 容器注册表中的某个项目的地址包括以下元素。 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -注册表主机的完全限定的名称。 在 Azure 容器注册表中的注册表主机的格式*myregistry*。 azurecr.io （全部小写）。 使用 Docker 或其他客户端工具到项目中请求或推送到 Azure 容器注册表时，必须指定 loginUrl。 
* **命名空间**-斜杠分隔的逻辑分组相关的映像或项目-例如，对于工作组中还是在应用程序
* **项目**-尤其是图像或项目的存储库的名称
* **标记**的特定版本的图像或存储在存储库的项目


例如，Azure 容器注册表中的映像的完整名称可能如下所示：

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

请参阅以下各节，有关这些元素的详细信息。

## <a name="repository-name"></a>存储库名称

管理容器注册表*存储库*，容器映像或其他项目具有相同名称但不同的标记的集合。 例如，以下三个映像位于“acr-helloworld”存储库中：

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

容器映像或在注册表中的其他项目与一个或多个标记相关联、 具有一个或多个层，和由一个清单。 了解每个这些组件之间的关系可以帮助您有效地管理您的注册表。

### <a name="tag"></a>标记

*标记*图像或其他项目指定它的版本。 在存储库中的单个项目可以分配一个或多个标记，也可能是"未标记"。 也就是说，可删除映像中的所有标记，而映像的数据（其层）保留在注册表中。

映像的名称由存储库（或存储库和命名空间）和标记进行定义。 在推送或拉取操作中指定映像名称，可以推送和拉取映像。

如何标记容器映像引导式开发或部署这些应用方案。 例如，为维护你的基本映像和部署映像的唯一标记建议稳定标记。 有关详细信息，请参阅[适用于标记和版本控制容器映像建议](container-registry-image-tag-version.md)。

### <a name="layer"></a>层

容器映像由一个或多个组成*层*，每个对应于定义该映像的 Dockerfile 中的行。 注册表中的映像共享常用层，从而提高存储效率。 例如，不同存储库中的多个映像可能共享同一个 Alpine Linux 基础层，但注册表中仅存储该层的一个副本。

由于多个映像共享常用层，层共享也可优化到节点的层分发。 例如，如果某节点上已有的映像包含 Alpine Linux 层作为其基础层，则在后续拉取引用同一层的不同映像时，不会将层传输到节点。 相反，它会引用节点上已存在的层。

若要提供安全隔离并可保护免受潜在层操作，层之间并不共享注册表。

### <a name="manifest"></a>清单

每个容器映像或推送到容器注册表的项目相关联*清单*。 推送映像时由注册表生成的清单唯一标识映像并指定其层。 可以列出使用 Azure CLI 命令的存储库的清单[az acr 存储库显示清单][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，清单摘要"acr helloworld"存储库的列表：

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

清单由唯一的 SHA-256 哈希（即清单摘要）进行标识  。 每个图像或项目-是否标记或不-标识由其摘要。 即便映像的层数据与其他映像的层数据相同，摘要值也是唯一的。 此机制使你能够反复向注册表推送标记相同的映像。 例如，你可反复向注册表推送 `myimage:latest` 而不出任何错误，因为每个映像均由其唯一摘要标识。

通过在拉取操作中指定映像的摘要，可从注册表拉取该映像。 某些系统可能配置为按摘要拉取，因为它保证即便后续向注册表推送标记相同的映像，仍将拉取映像版本。

例如，图像从"acr helloworld"存储库拉取通过清单摘要：

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 如果反复推送修改后的标记相同的映像，可能创建孤立的映像；此类映像不带标记，但仍占用注册表中的空间。 按标记列出或查看映像时，Azure CLI 或 Azure 门户中不显示无标记的映像。 但是，它们的层仍然存在，且占用注册表中的空间。 释放由未标记的图像所用空间的信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。


## <a name="next-steps"></a>后续步骤

详细了解如何[图像存储](container-registry-storage.md)并[支持的内容格式](container-registry-image-formats.md)Azure 容器注册表中。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


