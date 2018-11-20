---
title: Azure 容器注册表内容格式
description: 了解 Azure 容器注册表中支持的内容格式。
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634815"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure 容器注册表中支持的内容格式

使用 Azure 容器注册表中的专用存储库来管理以下内容格式之一。 

## <a name="docker-compatible-container-images"></a>兼容 Docker 的容器映像

* [Docker 映像清单 V2，架构 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像清单 V2，架构 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - 包括了允许注册表在单个“映像:标记”引用下存储多平台映像的清单列表

* [开放容器计划 (OCI) 映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Helm 图表

Azure 容器注册表还可以承载 [Helm 图表](https://helm.sh/)的存储库，该存储库是用来快速为 Kubernetes 管理和部署应用程序的一种打包格式。 支持 [Helm 客户端](https://docs.helm.sh/using_helm/#installing-helm)版本 2.11.0 或更高版本。

## <a name="next-steps"></a>后续步骤

* 请参阅如何使用 Azure 容器注册表[推送和拉取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 任务](container-registry-tasks-overview.md)来生成并测试容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 以 OCI 格式生成并打包容器。

* 设置 Azure 容器注册表中承载的 [Helm 存储库](container-registry-helm-repos.md)。 


