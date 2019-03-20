---
title: Azure 容器实例容器组
description: 了解如何多容器组在 Azure 容器实例中工作
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 8724bd7e13b0d8607ad5a6814b27c8c06681f331
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202004"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure 容器实例中的容器组

Azure 容器实例中的顶层资源是容器组。 本文介绍容器组的定义和它们支持的方案类型。

## <a name="how-a-container-group-works"></a>容器组工作原理

容器组是安排在同一主机上的容器集合。 容器组中的容器共享生命周期、 资源、 本地网络和存储卷。 它是在概念上与类似*pod*中[Kubernetes][kubernetes-pod]。

以下关系图显示了一个包含多个容器的容器组示例：

![容器组图][container-groups-example]

此示例容器组：

* 安排在一个主机上。
* 已分配 DNS 名称标签。
* 公开一个公共 IP 地址（只有一个公开端口）。
* 由两个容器组成。 其中一个容器侦听端口 80，另一个容器侦听端口 5000。
* 包含两个 Azure 文件共享作为卷装载，每个容器本地装载一个共享。

> [!NOTE]
> 多容器组目前支持仅 Linux 容器。 Windows 容器中的 Azure 容器实例仅支持单个实例的部署。 我们正致力于为 Windows 容器提供的所有功能，您可以了解在服务中的当前平台差异[概述](container-instances-overview.md#linux-and-windows-containers)。

## <a name="deployment"></a>部署

以下是部署多容器组的两种常见方式： 使用[资源管理器模板][ resource-manager template]或[YAML 文件][yaml-file]。 当您需要部署其他 Azure 服务资源时使用资源管理器模板 (例如， [Azure 文件共享][azure-files]) 时部署容器实例。 由于 YAML 格式更简洁的特性，YAML 文件时，建议你的部署包括仅容器实例。

## <a name="resource-allocation"></a>资源分配

Azure 容器实例分配资源，例如 Cpu、 内存，并选择性地[Gpu] [ gpus] （预览版） 到容器组通过添加[资源请求][resource-requests]的组中的实例。 作为示例，占用 CPU 资源，如果有两个实例，每个请求 1 创建的容器组 CPU，则为容器组分配了 2 个 Cpu。

可用于容器组的最大资源依赖于[Azure 区域][ region-availability]用于部署。

### <a name="container-resource-requests-and-limits"></a>容器资源请求和限制

* 默认情况下，在组中的容器实例共享的组的请求的资源。 具有两个组中实例的每个请求 1 CPU，作为整体组有权访问 2 个 Cpu。 每个实例可以最多使用 2 个 Cpu，实例可能会争夺 CPU 资源，在运行时。

* 若要限制的组中实例的资源使用量，根据需要设置[资源限制][ resource-limits]的实例。 具有两个实例的组中请求 1 CPU，一个容器可能需要更多的 Cpu 比其他运行。

  在此方案中，你可以设置资源限制为 0.5 个 CPU 一个实例和第二个 2 个 Cpu 的限制。 此配置限制为 0.5 的第一个容器的资源使用情况 CPU，允许最多使用完整 2 个 Cpu 如果可用的第二个容器。

有关详细信息，请参阅[ResourceRequirements] [ resource-requirements]容器中的属性组 REST API。

### <a name="minimum-and-maximum-allocation"></a>最小值和最大分配

* 分配**最小**1 的 CPU 和 1 GB 的内存与容器组。 小于 1 可预配组中的单个容器实例 CPU 和 1 GB 的内存。 

* 有关**最大**容器组中的资源，请参阅 [资源可用性] [aci--的区域可用性] 部署区域的 Azure 容器实例。

## <a name="networking"></a>网络

容器组共享 IP 地址和该 IP 地址上的端口命名空间。 若要启用外部客户端来访问组内的容器，必须从该容器公开 IP 地址上的端口。 由于组内的容器共享端口命名空间，不支持端口映射。 组内的容器可相互通过访问它们已公开的端口上的本地主机即使这些端口不组的 IP 地址上对外公开。

（可选） 部署到的容器组[Azure 虚拟网络][ virtual-network] （预览版） 来让容器与虚拟网络中的其他资源进行安全通信。

## <a name="storage"></a>存储

可以指定要在容器组内装载的外部卷。 可以将这些卷映射到组中单个容器内的特定路径。

## <a name="common-scenarios"></a>常见方案

如果希望将单个功能性任务分成少数容器映像，则多容器组十分有用。 然后，这些映像可以由不同的团队交付并具有单独的资源需求。

使用情况示例包括：

* 为 Web 应用程序提供服务的容器和从源控件拉取最新内容的容器。
* 应用程序容器和日志记录容器。 日志记录容器通过主要应用程序收集日志和指标输出并将其写入长期存储。
* 应用程序容器和监视容器。 监视容器定期向应用程序发送请求，确保该应用程序正常运行和响应，并在应用程序出现异常时发出警报。
* 前端容器和后端容器。 前端可能用作 web 应用程序，与后端运行的服务来检索数据。 

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 资源管理器模板部署多容器容器组：

> [!div class="nextstepaction"]
> [部署容器组][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
