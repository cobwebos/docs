---
title: Supported content formats
description: Learn about content formats supported by Azure Container Registry, including Docker-compatible container images, Helm charts, OCI images, and OCI artifacts.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455022"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure 容器注册表中支持的内容格式

使用 Azure 容器注册表中的专用存储库来管理以下内容格式之一。 

## <a name="docker-compatible-container-images"></a>兼容 Docker 的容器映像

The following Docker container image formats are supported:

* [Docker 映像清单 V2，架构 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像清单 V2，架构 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 包括了允许注册表在单个“映像:标记”引用下存储多平台映像的清单列表

## <a name="oci-images"></a>OCI images

Azure Container Registry supports images that meet the [Open Container Initiative (OCI) Image Format Specification](https://github.com/opencontainers/image-spec/blob/master/spec.md). Packaging formats include [Singularity Image Format (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI artifacts

Azure Container Registry supports the [OCI Distribution Specification](https://github.com/opencontainers/distribution-spec), a vendor-neutral, cloud-agnostic spec to store, share, secure, and deploy container images and other content types (artifacts). The specification allows a registry to store a wide range of artifacts in addition to container images. You use tooling appropriate to the artifact to push and pull artifacts. For an example, see [Push and pull an OCI artifact using an Azure container registry](container-registry-oci-artifacts.md).

To learn more about OCI artifacts, see the [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) repo and the [OCI Artifacts](https://github.com/opencontainers/artifacts) repo on GitHub.

## <a name="helm-charts"></a>Helm 图表

Azure Container Registry can host repositories for [Helm charts](https://helm.sh/), a packaging format used to quickly manage and deploy applications for Kubernetes. [Helm client](https://docs.helm.sh/using_helm/#installing-helm) version 2 (2.11.0 or later) is supported.

## <a name="next-steps"></a>后续步骤

* 请参阅如何使用 Azure 容器注册表[推送和拉取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 任务](container-registry-tasks-overview.md)来生成并测试容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 以 OCI 格式生成并打包容器。

* 设置 Azure 容器注册表中承载的 [Helm 存储库](container-registry-helm-repos.md)。 


