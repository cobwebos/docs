---
title: 托管容器注册表
description: 介绍 Azure 容器注册表服务，该服务提供基于云的托管专用 Docker 注册表。
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 1992a2a63d16a955d136459f5dbaece7df815c71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77132035"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure 中的专用 Docker 容器注册表简介

Azure 容器注册表是基于开源 Docker 注册表 2.0 的托管专用 Docker 注册表服务。 可以创建和维护 Azure 容器注册表来存储与管理专用的 Docker 容器映像和相关项目。

将 Azure 容器注册表与现有的容器开发和部署管道配合使用，也可使用 Azure 容器注册表任务在 Azure 中生成容器映像。 可以通过源代码提交和基础映像更新等触发器按需生成或完全自动生成。

有关 Docker 和注册表概念的详细信息，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)和[关于注册表、存储库和映像](container-registry-concepts.md)。

## <a name="use-cases"></a>用例

将 Azure 容器注册表中的映像提取到各种部署目标：

* **可缩放业务流程系统**，用于跨主机群集管理容器化应用程序，包括 [Kubernetes](https://kubernetes.io/docs/)、[DC/OS](https://docs.mesosphere.com/) 和 [Docker Swarm](https://docs.docker.com/swarm/)。
* 支持大规模生成和运行应用程序的 **Azure 服务**，包括 [Azure Kubernetes 服务 (AKS)](../aks/index.yml)、[应用服务](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](/azure/service-fabric/) 和其他服务。

开发人员还可以在执行容器开发工作流的过程中将内容推送到容器注册表。 例如，通过持续集成和交付工具（例如 [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) 或 [Jenkins](https://jenkins.io/)）将容器注册表作为目标。

配置 ACR 任务，在应用程序映像的基础映像发生更新时自动重新生成应用程序映像，或者在团队将代码提交到 Git 存储库时自动完成映像生成。 创建多步骤任务，在云中以并行方式自动完成多个容器映像的生成、测试和修补。

Azure 提供包括 Azure 命令行界面、Azure 门户和 API 支持在内的工具，用于管理 Azure 容器注册表。 可以选择安装[适用于 Visual Studio Code 的 Docker 扩展](https://code.visualstudio.com/docs/azure/docker)以及适用于 Azure 容器注册表的 [Azure 帐户](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)扩展。 通过 Azure 容器注册表拉取和推送映像，或者运行 ACR 任务，这一切都可以在 Visual Studio Code 中进行。

## <a name="key-features"></a>主要功能

* **注册表 SKU** - 在 Azure 订阅中创建一个或多个容器注册表。 注册表以三种 SKU 形式提供：[基本、标准和高级](container-registry-skus.md)，每一种都支持 Webhook 集成、通过 Azure Active Directory 进行的注册表身份验证，以及删除功能。 在与部署相同的 Azure 位置创建注册表，充分利用容器映像的本地闭合网络存储。 将高级注册表的[异地复制](container-registry-geo-replication.md)功能用于高级复制和容器映像分发方案。 

* **安全性和访问** - 使用 Azure CLI 或标准的 `docker login` 命令登录到注册表。 Azure 容器注册表通过 HTTPS 传输容器映像，并支持通过 TLS 来保护客户端连接。 

  > [!IMPORTANT]
  > 从 2020 年 1 月 13 日开始，Azure 容器注册表将要求服务器和应用程序的所有安全连接都使用 TLS 1.2。 使用任何最近的 Docker 客户端（版本 18.03.0 或更高版本）启用 TLS 1.2。 对 TLS 1.0 和 1.1 的支持将停用。 

  可以使用 Azure 标识、Azure Active Directory 支持的[服务主体](../active-directory/develop/app-objects-and-service-principals.md)或提供的管理员帐户来[控制访问](container-registry-authentication.md)（针对容器注册表）。 使用基于角色的访问控制 (RBAC) 向用户或系统分配对注册表的精细权限。

  高级 SKU 的安全功能包括用于映像标记签名的[内容信任](container-registry-content-trust.md)，以及用于限制对注册表的访问的[防火墙和虚拟网络（预览版）](container-registry-vnet.md)。 Azure 安全中心可以选择与 Azure 容器注册表集成，以便在将映像推送到注册表时[扫描映像](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)。

* **支持的映像和项目** - 每个映像都在存储库中进行了分组，是兼容 Docker 的容器的只读快照。 Azure 容器注册表可以包含 Windows 和 Linux 映像。 可以控制所有容器部署的映像名称。 使用标准 [Docker 命令](https://docs.docker.com/engine/reference/commandline/)可将映像推送到存储库，或者从存储库中提取映像。 除了 Docker 容器映像外，Azure 容器注册表还存储[相关的内容格式](container-registry-image-formats.md)，例如 [Helm 图表](container-registry-helm-repos.md)和为[开放容器计划 (OCI) 映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)构建的映像。

* **自动生成映像** - 使用 [Azure 容器注册表任务](container-registry-tasks-overview.md)（ACR 任务）可简化在 Azure 中生成、测试、推送和部署映像的过程。 例如，使用 ACR 任务可以通过将 `docker build` 操作产生的负荷转移到 Azure 来将开发内部循环扩展到云。 配置生成任务以使其自动执行容器 OS 和框架修补管道，并使其在团队将代码提交到源代码管理时自动生成映像。

  [多步骤任务](container-registry-tasks-overview.md#multi-step-tasks)提供用于在云中构建、测试和修补容器映像的基于步骤的任务定义和执行。 任务步骤定义各个容器映像构建和推送操作。 它们还可以定义一个或多个容器的执行，每个步骤都使用容器作为其执行环境。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建容器注册表](container-registry-get-started-portal.md)
* [使用 Azure CLI 创建容器注册表](container-registry-get-started-azure-cli.md)
* [使用 ACR 任务自动执行容器生成和维护](container-registry-tasks-overview.md)