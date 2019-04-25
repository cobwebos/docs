---
title: Azure 中的专用 Docker 容器注册表 - 概述
description: 介绍 Azure 容器注册表服务，该服务提供基于云的托管专用 Docker 注册表。
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ce870bfb8d29f7a808962e4d273388ab31186f10
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997389"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Azure 中的专用 Docker 容器注册表简介

Azure 容器注册表是基于开源 Docker 注册表 2.0 的托管 [Docker 注册表](https://docs.docker.com/registry/)服务。 可以创建和维护 Azure 容器注册表来存储与管理专用的 [Docker 容器](https://www.docker.com/what-docker)映像。

可将 Azure 中的容器注册表与现有的容器开发和部署管道配合使用，也可使用 [ACR 任务](#azure-container-registry-tasks)在 Azure 中生成容器映像。 可以通过源代码提交和基础映像更新生成触发器按需生成或完全自动生成。

有关 Docker 和容器的背景信息，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。

## <a name="use-cases"></a>用例

将 Azure 容器注册表中的映像提取到各种部署目标：

* **可缩放业务流程系统**，用于跨主机群集管理容器化应用程序，包括 [Kubernetes](https://kubernetes.io/docs/)、[DC/OS](https://docs.mesosphere.com/) 和 [Docker Swarm](https://docs.docker.com/swarm/)。
* 支持大规模生成和运行应用程序的 **Azure 服务**，包括 [Azure Kubernetes 服务 (AKS)](../aks/index.yml)、[应用服务](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](/azure/service-fabric/) 和其他服务。

开发人员还可以在执行容器开发工作流的过程中将内容推送到容器注册表。 例如，通过连续集成和部署工具（例如 [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) 或 [Jenkins](https://jenkins.io/)）将目标设置为容器注册表。

配置 ACR 任务，在应用程序映像的基础映像发生更新时自动重新生成应用程序映像，或者在团队将代码提交到 Git 存储库时自动完成映像生成。 创建多步骤任务，在云中以并行方式自动完成多个容器映像的生成、测试和修补。

Azure 提供包括 Azure 命令行界面、Azure 门户和 API 支持在内的工具，用于管理 Azure 容器注册表。 可以选择安装[适用于 Visual Studio Code 的 Docker 扩展](https://code.visualstudio.com/docs/azure/docker)以及适用于 Azure 容器注册表的 [Azure 帐户](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)扩展。 通过 Azure 容器注册表拉取和推送映像，或者运行 ACR 任务，这一切都可以在 Visual Studio Code 中进行。

## <a name="key-concepts"></a>关键概念

* **注册表** - 在 Azure 订阅中创建一个或多个容器注册表。 注册表以三种 SKU 形式提供：[基本、标准和高级](container-registry-skus.md)，每一种都支持 Webhook 集成、通过 Azure Active Directory 进行的注册表身份验证，以及删除功能。 在与部署相同的 Azure 位置创建注册表，充分利用容器映像的本地闭合网络存储。 将高级注册表的[异地复制](container-registry-geo-replication.md)功能用于高级复制和容器映像分发方案。 完全限定的注册表名称采用以下格式：`myregistry.azurecr.io`。

  可以使用 Azure 标识、Azure Active Directory 支持的[服务主体](../active-directory/develop/app-objects-and-service-principals.md)或提供的管理员帐户来[控制访问](container-registry-authentication.md)（针对容器注册表）。 使用 Azure CLI 或标准的 `docker login` 命令登录到注册表。

* **存储库** - 一个注册表包含一个或多个存储库，该库是包含容器映像的虚拟组，而这些映像使用相同的名称但不同的标记或摘要。 Azure 容器注册表支持多级存储库命名空间。 使用多级命名空间可将特定应用相关的映像集合分组，或者将特定开发或运营团队的应用集合分组。 例如：

  * `myregistry.azurecr.io/aspnetcore:1.0.1` 表示企业范围的映像
  * `myregistry.azurecr.io/warrantydept/dotnet-build` 表示用于构建 .NET 应用、在保修部门之间共享的映像
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` 表示一个 Web 映像，它已在客户提交应用中分组，由保修部门拥有

* **映像** - 存储在存储库中，每个映像是兼容 Docker 的容器的只读快照。 Azure 容器注册表可以包含 Windows 和 Linux 映像。 可以控制所有容器部署的映像名称。 使用标准 [Docker 命令](https://docs.docker.com/engine/reference/commandline/)可将映像推送到存储库，或者从存储库中提取映像。 除了 Docker 容器映像外，Azure 容器注册表还存储[相关的内容格式](container-registry-image-formats.md)，例如 [Helm 图表](container-registry-helm-repos.md)和为[开放容器计划 (OCI) 映像格式规范](https://github.com/opencontainers/image-spec/blob/master/spec.md)构建的映像。

* **容器** - 容器定义软件应用程序及其在完整文件系统中包装的依赖项，包括代码、运行时、系统工具和库。 可以基于从容器注册表提取的 Windows 或 Linux 映像运行 Docker 容器。 在一台计算机上运行的容器共享操作系统内核。 Docker 容器完全可移植到所有主要 Linux 发行版、macOS 和 Windows。

## <a name="azure-container-registry-tasks"></a>Azure 容器注册表任务

[Azure 容器注册表任务](container-registry-tasks-overview.md)（ACR 任务）是 Azure 容器注册表中的一个功能套件，用于在 Azure 中提供简化且高效的 Docker 容器映像生成功能。 使用 ACR 任务可以通过将 `docker build` 操作卸载到 Azure 来将开发内部循环扩展到云。 配置生成任务以使其自动执行容器 OS 和框架修补管道，并使其在团队将代码提交到源代码管理时自动生成映像。

[多步骤任务](container-registry-tasks-overview.md#multi-step-tasks)提供用于在云中构建、测试和修补容器映像的基于步骤的任务定义和执行。 任务步骤定义各个容器映像构建和推送操作。 它们还可以定义一个或多个容器的执行，每个步骤都使用容器作为其执行环境。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建容器注册表](container-registry-get-started-portal.md)
* [使用 Azure CLI 创建容器注册表](container-registry-get-started-azure-cli.md)
* [使用 ACR 任务自动执行 OS 和框架修补](container-registry-tasks-overview.md)
