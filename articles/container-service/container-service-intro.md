---
title: "Azure 云中的 Docker 容器群集 | Microsoft 文档"
description: "Azure 容器服务提供了一种简化创建、配置和管理预配置来运行容器化应用程序的虚拟机群集的方法。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: 52da4163-1182-4b2e-be00-4951e5c1da16
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: cc3cc2dce324942f184b6a520dc4db28518a3091
ms.openlocfilehash: 0521fbd689233c0f2359a10006e24c75c8997732


---
# <a name="azure-container-service-introduction"></a>Azure 容器服务简介
Azure 容器服务让创建、配置和管理预配置来运行容器化应用程序的虚拟机群集变得更简单。 它使用热门开源计划和业务流程工具的优化配置。 通过此服务，用户可使用现有技能或利用不断增加的大量社区专业知识，在 Microsoft Azure 上部署和管理基于容器的应用程序。

![Azure 容器服务提供了在 Azure 的多个主机上管理容器化应用程序的方法。](./media/acs-intro/acs-cluster-new.png)

Azure 容器服务利用 Docker 容器格式，确保应用程序容器完全可移植。 它也支持选择 Marathon 和 DC/OS、Docker Swarm 或 Kubernetes，使用户能够将这些应用程序扩展到数千个，甚至数万个容器中。

>[!NOTE]
> Azure 容器服务中的 Kubernetes 支持当前为预览版。
>

通过 Azure 容器服务，可利用 Azure 的企业级功能，同时保留应用程序的可移植性，包括业务流程层的可移植性。

## <a name="using-azure-container-service"></a>使用 Azure 容器服务
Azure 容器服务旨在通过使用当今客户中热门的开源工具和技术提供容器托管环境。 为此，我们为用户所选的业务流程（DC/OS、Docker Swarm 或 Kubernetes）公开了标准 API 终结点。 通过使用这些终结点，可利用能够与这些终结点通信的任何软件。 例如，对于 Docker Swarm 终结点，可选择使用Docker 命令行接口 (CLI)。 对于 DC/OS，可以选择使用 DCOS CLI。 对于 Kubernetes，可以选择使用 kubectl。

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>通过 Azure 容器服务创建 Docker 群集
若要开始使用 Azure 容器服务，可通过门户（搜索“Azure 容器服务”），使用 Azure Resource Manager 模板（[Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)、[DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) 或 [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)）或使用 [CLI](/articles/xplat-cli-install.md) 部署 Azure 容器服务群集。 可修改提供的快速入门模板，包含其他或高级 Azure 配置。 有关部署 Azure 容器服务群集的详细信息，请参阅[部署 Azure 容器服务群集](container-service-deployment.md)。

## <a name="deploying-an-application"></a>部署应用程序
Azure 容器服务针对业务流程提供了 Docker Swarm、DC/OS 或 Kubernetes 选项。 部署应用程序的方式取决于所选的业务流程。

### <a name="using-dcos"></a>使用 DC/OS
DC/OS 是基于 Apache Mesos 分布式系统内核的分布式操作系统。 Apache Mesos 存放于 Apache Software Foundation，并将一些 [IT 业界知名人士](http://mesos.apache.org/documentation/latest/powered-by-mesos/) 列为用户和参与者。

![为显示代理和主机的 Swarm 配置的 Azure 容器服务。](media/acs-intro/dcos.png)

DC/OS 和 Apache Mesos 包含一个引人注目的功能集：

* 成熟的可伸缩性
* 使用 Apache ZooKeeper 的容错复制主机和从属服务器
* 支持 Docker 格式的容器
* 任务与 Linux 容器间的本机隔离
* 多资源计划（内存、CPU、磁盘和端口）
* 用于开发新并行应用程序的 Java、Python 和 C++ API
* 用于查看群集状态的 Web UI

默认情况下，Azure 容器服务上运行的 DC/OS 包括用于计划工作负荷的 Marathon 业务流程平台。 但是，ACS 的 DC/OS 部署随附的是可添加到用户的服务的服务 Mesosphere Universe，包括 Spark、Hadoop、Cassandra 等。

![Azure 容器服务中的 DC/OS Universe](media/dcos/universe.png)

#### <a name="using-marathon"></a>使用 Marathon
Marathon 是 cgroups 中针对服务的群集范围初始化和控制系统，或者对于 Azure 容器服务而言，是 Docker 格式的容器。 Marathon 提供可从中部署应用程序的 Web UI。 可通过类似于 `http://DNS_PREFIX.REGION.cloudapp.azure.com` 的 URL 访问它，其中 DNS\_PREFIX 和 REGION 均在部署时定义。 当然，还可以提供自己的 DNS 名称。 有关使用 Marathon Web UI 运行容器的详细信息，请参阅[通过 Web UI 管理容器](container-service-mesos-marathon-ui.md)。

![Marathon 应用程序列表](media/dcos/marathon-applications-list.png)

还可将 REST API 用于与 Marathon 通信。 有大量客户端库可用于每个池。 它们涵盖多种语言，当然，用户可以使用任何语言的 HTTP 协议。 此外，许多热门 DevOps 工具提供对 Marathon 的支持。 处理 Azure 容器服务群集时，这可为运营团队提供最大的灵活性。 有关使用 Marathon REST API 运行容器的详细信息，请参阅[通过 REST API 管理容器](container-service-mesos-marathon-rest.md)。

### <a name="using-docker-swarm"></a>使用 Docker Swarm
Docker Swarm 为 Docker 提供本机群集。 由于 Docker Swarm 为标准 Docker API 提供服务，因此任何已与 Docker 守护程序通信的工具均可使用 Swarm 以透明方式扩展到 Azure 容器服务上的多个主机。

![配置来使用 DC/OS 的 Azure 容器服务 -- 显示 jumpbox、代理和主机。](media/acs-intro/acs-swarm2.png)

支持用于管理 Swarm 群集上容器的工具包括但不限于以下工具：

* Dokku
* Docker CLI 和 Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>使用 Kubernetes
Kubernetes 是一种常用的开源生产级容器业务流程工具，用于自动化容器部署，扩展和管理容器化应用程序。 由于它是开源解决方案且由开源社区驱动，因此可在 Azure 容器服务中无缝运行，并可用于在 Azure 容器服务中大规模部署容器。

![Azure 容器服务配置为使用 Kubernetes -- 显示代理和主机。](media/acs-intro/kubernetes.png)

它具有一组丰富的功能，包括：
* 水平扩展
* 服务发现和负载均衡
* 机密和配置管理
* 基于 API 的自动推出和回滚
* 自我修复
* 及更多。



## <a name="videos"></a>视频
Azure 容器服务入门 (101)：  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

使用 Azure 容器服务生成应用程序 (Build 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>



<!--HONumber=Feb17_HO1-->


