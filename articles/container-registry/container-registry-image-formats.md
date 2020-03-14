---
title: 支持的内容格式
description: 了解 Azure 容器注册表支持的内容格式，其中包括 Docker 兼容容器映像、Helm 图、OCI 图像和 OCI 项目。
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247002"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure 容器注册表中支持的内容格式

使用 Azure 容器注册表中的专用存储库来管理以下内容格式之一。 

## <a name="docker-compatible-container-images"></a>兼容 Docker 的容器映像

支持以下 Docker 容器映像格式：

* [Docker 映像清单 V2，架构 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像清单 V2，架构 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 包括了允许注册表在单个“映像:标记”引用下存储多平台映像的清单列表

## <a name="oci-images"></a>OCI 图像

Azure 容器注册表支持满足[开放式容器计划（OCI）映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)的映像。 打包格式包括[奇点映像格式（SIF）](https://github.com/sylabs/sif)。

## <a name="oci-artifacts"></a>OCI 项目

Azure 容器注册表支持[OCI 分发规范](https://github.com/opencontainers/distribution-spec)，这是一种特定于供应商的、与云无关的规范，可用于存储、共享、保护和部署容器映像及其他内容类型（项目）。 除了容器映像以外，该规范还允许注册表存储各种不同的项目。 使用适用于项目的工具来推送和拉取项目。 有关示例，请参阅[使用 Azure 容器注册表推送和拉取 OCI 项目](container-registry-oci-artifacts.md)。

若要了解有关 OCI 项目的详细信息，请参阅 GitHub 上的[OCI 注册表作为存储（ORAS）存储](https://github.com/deislabs/oras)库和[OCI 项目](https://github.com/opencontainers/artifacts)存储库。

## <a name="helm-charts"></a>Helm 图表

Azure 容器注册表可以托管[Helm 图](https://helm.sh/)的存储库，这是用于快速管理和部署 Kubernetes 应用程序的打包格式。 支持[Helm client](https://docs.helm.sh/using_helm/#installing-helm)版本2（2.11.0 或更高版本）。

## <a name="next-steps"></a>后续步骤

* 请参阅如何使用 Azure 容器注册表[推送和拉取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 任务](container-registry-tasks-overview.md)来生成并测试容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 以 OCI 格式生成并打包容器。

* 设置 Azure 容器注册表中承载的 [Helm 存储库](container-registry-helm-repos.md)。 


